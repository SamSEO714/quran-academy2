# Rehal Quran Academy website (a project of Pajee Foundation)

A complete website for an online Quran academy: the public site that brings in trial requests, dashboards for students, parents and teachers, and an admin panel that runs it all.

- **Framework:** Next.js 16 (App Router), React 19, Tailwind CSS 4, TypeScript
- **Hosting:** Vercel, with the code on GitHub
- **Database:** Postgres (Neon through Vercel works out of the box)
- **Languages:** English, Urdu and Arabic (right-to-left handled)
- **Public pages:** 79, all pre-rendered as static HTML for speed (33 in English, 23 each in Urdu and Arabic)
- **Class types:** one-to-one, family group (2 to 4 learners) and community group (3 to 6 learners)

> "Rehal Quran Academy" and "Pajee Foundation" are **temporary names**. The academy name, the parent organisation ("A project of ...") and the address `quran.pajee.org` are all set in one file: `src/content/site.ts`.

---

## 1. Run it on your computer

```bash
npm install
cp .env.example .env.local     # then open .env.local and set ADMIN_PASSWORD and AUTH_SECRET
npm run dev
```

Open http://localhost:3000.

| Address | Who it is for |
|---|---|
| `/admin` | You. Log in with `ADMIN_PASSWORD`. |
| `/signup` and `/login` | Students, parents, group organisers and teachers |
| `/portal` | Student and parent dashboard |
| `/teach` | Teacher dashboard |

Without a `DATABASE_URL`, everything is saved in a small database inside the `.data` folder on your computer, so you can try the whole thing first. Delete that folder to start again with empty data.

## 2. Put it live (GitHub + Vercel)

1. Create a new **empty** repository on GitHub.
2. In this folder run:
   ```bash
   git init
   git add .
   git commit -m "Quran academy website"
   git branch -M main
   git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPO.git
   git push -u origin main
   ```
   Use these commands rather than uploading through the GitHub website. Drag-and-drop upload can flatten the folders, and the site will not build.
3. On vercel.com choose **Add New > Project**, import the repository and press **Deploy**. No build settings need changing.
4. In the Vercel project open **Storage > Create Database > Neon (Postgres)** and connect it to the project. This adds `DATABASE_URL` for you. All tables are created automatically on the first request. Without a database nothing can be saved on Vercel: no trial requests, no accounts, no classes.
5. In **Settings > Environment Variables** add the values below, then **Redeploy**.

| Variable | Needed | What it is |
|---|---|---|
| `NEXT_PUBLIC_SITE_URL` | Yes | `https://quran.pajee.org`. Used for canonical URLs, the sitemap and schema. |
| `ADMIN_PASSWORD` | Yes | Password for `/admin`. The admin panel stays locked until this is set. |
| `AUTH_SECRET` | Yes | Any long random text. It signs the login cookies for the admin panel and for every student, parent and teacher. Changing it later signs everyone out. |
| `DATABASE_URL` | Yes | Added by the Neon integration. |
| `NEXT_PUBLIC_WHATSAPP` | Recommended | WhatsApp number, digits only with country code. Empty hides every WhatsApp button. |
| `NEXT_PUBLIC_CONTACT_EMAIL` | Recommended | Public email. Empty hides it. |
| `RESEND_API_KEY`, `LEAD_ALERT_TO`, `LEAD_ALERT_FROM` | Recommended | Email alert for each new lead, sent through resend.com. |
| `LEAD_WEBHOOK_URL` | Optional | Posts each lead as JSON to Zapier, Make or a Google Sheet script. |
| `NEXT_PUBLIC_GA_ID` | Optional | Google Analytics 4 ID (`G-XXXX`). |
| `NEXT_PUBLIC_META_PIXEL_ID` | Optional | Meta Pixel ID. |
| `NEXT_PUBLIC_GOOGLE_ADS_ID`, `NEXT_PUBLIC_GOOGLE_ADS_LEAD_LABEL` | Optional | Google Ads conversion (`AW-XXXX` and the label). |
| `GOOGLE_SITE_VERIFICATION`, `BING_SITE_VERIFICATION` | Optional | Search Console and Bing Webmaster verification codes. |
| `ADMIN_TIMEZONE` | Optional | Time zone for dates in the dashboard, e.g. `Asia/Karachi`. |

6. Connect the subdomain:
   1. In Vercel open **Settings > Domains**, type `quran.pajee.org` and press **Add**.
   2. Vercel shows a **CNAME** record. Where the DNS for `pajee.org` is managed (your domain registrar or Cloudflare), add it: type `CNAME`, name `quran`, value exactly as Vercel shows it.
   3. Wait until Vercel shows the domain as valid. It issues the HTTPS certificate by itself.
   4. Do not point the main `pajee.org` record at this project. Only the `quran` subdomain belongs to the academy.

## 3. Before you launch: make it yours

Everything a visitor reads comes from the files in `src/content/`. No page code needs touching.

| File | What you change there |
|---|---|
| `site.ts` | Academy name, parent organisation name, WhatsApp, email, address, social links, trial offer, teaching languages, sibling discount, **your story**, **your teachers**, **your reviews** |
| `pricing.ts` | Monthly fees in USD, GBP, AUD and CAD, for one-to-one, family group and community group classes |
| `courses.ts` | The 8 course pages |
| `pages.ts` | Country pages (USA, UK, Australia, Canada), audience pages (kids, adults, female teachers) and the group classes page |
| `faqs.ts` | Questions and answers |
| `quizzes.ts` | The ready-made quiz bank (9 quizzes, 93 questions) |
| `blog.ts` | Guide articles (English only) |
| `legal.ts` | Privacy policy, terms, refund policy (English only) |
| `i18n/*.ur.ts`, `i18n/*.ar.ts` | The Urdu and Arabic versions of courses, pages and FAQs |

Short pieces of text (menus, buttons, form labels, headings) are in `src/i18n/`: `ui.*.ts` for the public site and `app.*.ts` for the dashboards, one file per language.

**Check these six things. They are statements about your academy, so they must be true:**

1. **Fees** in `pricing.ts`, including the group fees. The figures are a competitive starting point, not your decision. Group fees are set so that a family group of two still pays the teacher at least the one-to-one hourly rate.
2. **Teacher statements** on the About page and in `faqs.ts` (how teachers are selected, female teachers, Ijazah holders, English).
3. **Policies**: 3 free trial classes, no registration fee, 10% sibling discount, missed-class and refund rules.
4. **Legal pages** in `legal.ts`. Now that the site holds accounts and children's lesson records, have the privacy policy checked for the countries you serve (UK and EU data protection law, COPPA in the USA).
5. **The Urdu and Arabic text.** It was written carefully but has not been read by a native-speaking editor. Have one person per language read the main pages before you advertise in that language.
6. **The quiz bank** in `quizzes.ts`. It only contains facts taught the same way everywhere, but your head teacher should read all 93 questions once.

The site shows **no reviews, student numbers or star ratings** because none have been supplied. Add real reviews to `site.ts` and the reviews section and rating schema appear automatically. Do not add invented ones: Google penalises fake review markup and families can tell.

## 4. The admin panel (`/admin`)

- **Overview:** leads waiting for a reply, last 7 and 30 days, trials, enrolments, leads per day, and breakdowns by pipeline stage, source, country, course, page and campaign.
- **Leads:** search and filter by status, country and source.
- **Lead page:** one-tap WhatsApp, call and email. Shows the family's local time, their request, and exactly how they found you (UTM tags, Google and Meta click IDs, referrer, first page visited). Set the status and keep notes.
- **Download CSV:** every lead with every field.

Pipeline stages: New, Contacted, Trial booked, Trial done, Enrolled, Lost.

- **People:** every account. Teacher applications wait here: open one, check it, then press **Approve** or **Decline**. You can pause any account, set a new password for someone who has forgotten theirs, or delete an account.
- **Classes:** create a class (one-to-one, family group or community group), choose the teacher, add the learners, and paste the Zoom or Meet link. Learners who are not in any class are listed at the bottom.
- **Performance:** one row per teacher (classes, learners, lessons in the last 30 days, attendance, learners' quiz average, last lesson) and one row per learner (attendance, lessons, teacher's mark, quiz average, oral tests). Low attendance and teachers who have recorded nothing for 14 days are flagged in words.

**Tracking ad campaigns:** tag every ad link, for example
`https://quran.pajee.org/online-quran-classes-uk?utm_source=facebook&utm_medium=paid&utm_campaign=uk-mums-video`.
The tags are remembered while the visitor browses and saved with the lead, so the dashboard shows which campaign produced which enrolment.

## 5. Accounts and dashboards

**Who can sign up (`/signup`):**

| Account | For | What happens |
|---|---|---|
| Parent or group organiser | A parent, or the person organising a community group | Active at once. They add each learner in their dashboard. |
| Student (16+) | Adults and older teenagers learning for themselves | Active at once. Under-16s are asked to have a parent sign up. |
| Teacher (male or female) | Teachers applying to teach | **Waits for your approval.** Until then the teacher sees only a "being reviewed" screen: no class, no learner, no family details. |

A family or student who signs up without using the trial form is also added to **Leads** (once), so somebody calls them.

**The usual flow:** family signs up and adds learners → you approve a teacher → you create a class, choose the teacher and add the learners → the class appears on both dashboards.

**Student and parent dashboard (`/portal`):** each learner's class, teacher, days and time, and a **Join class** button; attendance, number of lessons, the teacher's mark out of 5, quiz average; homework from the last lesson; the full lesson record with the teacher's notes; quizzes set by the teacher; practice quizzes; oral test marks.

**Teacher dashboard (`/teach`):** their classes and their own numbers; for each class, the learners with their statistics; **Record a lesson** (date, what was covered, homework, and for each learner attendance, a mark from 1 to 5 and a note for the family); **Set a quiz** for the class with an optional due date; **Record an oral test**; each learner's progress page; **Write a quiz** in any language. Teachers never see a family's phone number or email.

**Quizzes:** multiple choice, marked automatically. The answers stay on the server until the learner presses Finish, then every question is shown with the right answer. The ready-made bank is in English. Teachers' own quizzes can be in any language and can only be taken by the class they were set for.

**Things to know:**

- There is no "forgot password" email. The person messages you and you set a new password under **People**. (Adding reset emails is a small job once Resend is set up.)
- Email addresses are not verified at sign-up. That is fine while you phone or message every new family anyway; add email verification before you rely on email alone.
- Quizzes are taken at home, so treat the scores as practice, not as an exam. The oral test in class is the real check. Only a learner's **first** try at each quiz counts towards the quiz average; a second try after seeing the answers is saved as practice.
- A learner can be in more than one class. Each teacher sees only the lessons, tests and quizzes from their own classes; you see everything under **Performance**.
- A class set to "Ended" disappears from both dashboards but keeps its records. A teacher's own quiz cannot be deleted once learners have taken it.
- Wrong-password tries are counted in the database: 10 per email and 30 per connection in 15 minutes, then a 15-minute wait.
- Without `AUTH_SECRET` on the live site, nobody can sign up or log in. The admin panel shows a red notice if it is missing.
- The dashboards follow the language chosen with the EN / اردو / العربية buttons. The admin panel is always in English.
- Payments are not handled on the site.

## 6. Languages

- English pages have the plain address (`/pricing`). Urdu and Arabic have a prefix (`/ur/pricing`, `/ar/pricing`).
- Translated: home, courses, fees, free trial, FAQ, about, contact, the four country pages, kids, adults, female teachers, group classes, and both dashboards.
- English only: the guide articles and the three policy pages.
- Every translated page tells Google about its other language versions (`hreflang`), and the sitemap lists all of them.
- To change a translation, edit the matching file in `src/content/i18n/` or `src/i18n/`. If you change a fee or a policy in English, the Urdu and Arabic pages pick up the new **numbers** automatically, but any sentence you rewrite needs rewriting in the other two files as well.

## 7. Add content

**A new guide article:** copy one object in `src/content/blog.ts`, change the `slug`, write the `answer` (40 to 60 words that answer the title question on their own) and the `blocks`. It gets its own page, schema, sitemap entry and a line in `/llms.txt` automatically.

**A new country or city page:** copy a country object in `src/content/pages.ts` and change the slug, text, time zones and FAQs. Only add a page when you can write something specific to that place. Thin copies with the city name swapped do not rank.

**A new course:** add an object in `src/content/courses.ts`.

After any change: `git add . && git commit -m "Update content" && git push`. Vercel redeploys by itself.

## 8. What is built in for SEO, GEO and AEO

- One keyword target per page, in the URL, title, H1 and first paragraph
- A direct 40 to 60 word answer at the top of every page
- Schema: EducationalOrganization, WebSite, Course, Service, FAQPage, BreadcrumbList, BlogPosting, OfferCatalog (one-to-one and group fees)
- Log-in, sign-up, dashboard and admin pages are marked "do not index"
- `hreflang` between the USA, UK, Australia and Canada pages, and between the English, Urdu and Arabic version of every page
- `/sitemap.xml`, `/robots.txt` (search and AI crawlers allowed), `/llms.txt`
- Fees, time zones and comparisons in real HTML tables
- Static pages, self-hosted fonts, no images to download, no tracking until consent
- Cookie consent that gates Google Analytics, Google Ads and Meta Pixel

See `docs/SEO-PLAYBOOK.md` for the competitor analysis and the plan for ranking.
