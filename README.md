# UIU CSE Question Bank — Admin Backend

The API server behind the [QB_Admin](https://github.com/Hameme21/QB_Admin) portal. It handles Firebase-authenticated moderation actions (approve/reject/delete) on student-submitted question bank materials, and notifies submitters by email.

## What it does

- Signs Cloudinary uploads for the admin portal
- Verifies Firebase ID tokens on every privileged request and checks the caller's email against an `ADMIN_EMAILS` allowlist before allowing any moderation action
- Approves submissions by re-tagging their Cloudinary assets (`pending-review` → `approved`)
- Deletes rejected or removed submissions' files from Cloudinary
- Sends approval/rejection emails to students via SMTP (Nodemailer)

## API Endpoints

| Method | Route | Description |
|---|---|---|
| GET | `/api/health` | Health check + config status |
| GET | `/api/cloudinary/config` | Public Cloudinary config (cloud name, folder) |
| POST | `/api/cloudinary/sign-upload` | Returns a signed payload for a direct-to-Cloudinary upload |
| POST | `/api/cloudinary/delete` | *(admin only)* Permanently deletes a Cloudinary asset |
| POST | `/api/cloudinary/approve-assets` | *(admin only)* Marks assets as approved |
| POST | `/api/questions/notify` | *(admin only)* Emails a submitter about approval/rejection |

All admin-only routes require an `Authorization: Bearer <Firebase ID token>` header, and the token's email must appear in `ADMIN_EMAILS`.

## Tech Stack

- **Runtime:** Node.js (built-in `http` module — no framework)
- **Auth:** Firebase Identity Toolkit (token verification)
- **File storage:** Cloudinary (signed uploads, tagging, deletion)
- **Email:** Nodemailer over SMTP
- **Hosting:** Render

## Environment Variables

```env
PORT=4175
CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=
CLOUDINARY_UPLOAD_FOLDER=uiu-toolkits/question-bank
FIREBASE_WEB_API_KEY=
ADMIN_EMAILS=admin1@uiu.ac.bd,admin2@uiu.ac.bd
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=
SMTP_PASS=
```

## Getting Started

```bash
git clone https://github.com/Hameme21/QB_admin_backend.git
cd QB_admin_backend
npm install
# create a .env file with the variables above, or set them in your shell
npm start
```

The server starts on `http://localhost:4175` by default.

## Related Projects

- [QB_Admin](https://github.com/Hameme21/QB_Admin) — the admin portal frontend this API serves
- [UIU_Question_Bank](https://github.com/Hameme21/UIU_Question_Bank) — the public-facing question bank moderated through this system
- [Question_Bank_backend](https://github.com/Hameme21/Question_Bank_backend) — the sibling backend for the public-facing app

## License

Apache-2.0
