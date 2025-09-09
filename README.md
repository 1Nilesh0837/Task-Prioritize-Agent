# Task Prioritizer Agent

A secure agent that unifies tasks from Notion, GitHub, Google Calendar, and Asana, ranks them using simple AI/heuristics, and delivers a prioritized daily digest and unified dashboard.

## Setup

1. **Install Node.js**: Download and install from [https://nodejs.org](https://nodejs.org) (version 18 or higher recommended).

2. **Install dependencies**:
   ```bash
   npm install
   ```

3. **Descope Setup** (Required for GitHub/Google integration):
   - Create a **FREE** Descope account at [https://app.descope.com](https://app.descope.com)
   - Create a new **Project**
   - Go to **Authentication** → **Inbound Apps** → Create **Email OTP** or **Social Login**
   - Go to **Outbound Apps** → Create apps for:
     - **GitHub**: Add scopes `repo`, `public_repo`, `read:user`, `read:org`
     - **Google Calendar**: Add scopes `https://www.googleapis.com/auth/calendar.readonly`, `https://www.googleapis.com/auth/calendar.events`
   - Go to **Flows** → Create flows named `github-connect` and `google-connect`
   - **Configure Redirect URIs** in each Outbound App:
     - Add: `http://localhost:3000/api/connect/callback`
   - **Allowed Origins** in Project Settings:
     - Add: `http://localhost:3000`
   - Copy your **Project ID** and **Management Key** from Settings

4. **Environment Variables**:
   Create a `.env.local` file in the root directory:
   ```
   DESCOPE_PROJECT_ID=your_actual_descope_project_id
   DESCOPE_MANAGEMENT_KEY=your_actual_management_key
   NEXT_PUBLIC_DESCOPE_PROJECT_ID=your_actual_descope_project_id
   ```

5. **Database Setup**:
   ```bash
   npx prisma generate
   npx prisma db push
   ```

6. **Run the Application**:
   ```bash
   npm run dev
   ```
   Open [http://localhost:3000](http://localhost:3000) in your browser.

## 🔗 Connecting Accounts

1. Visit [http://localhost:3000](http://localhost:3000)
2. Click "Get Started" → "Connect GitHub" or "Connect Google Calendar"
3. You'll be redirected to `/api/connect/[provider]` → then to Descope's OAuth flow
4. Grant permissions to access your GitHub issues or Google Calendar
5. Descope redirects to `/api/connect/callback` with auth code
6. Connection is stored in database and you see success message
7. Tasks are automatically fetched and displayed in dashboard

## 🧪 Testing the OAuth Flow

- **Connect Route**: `GET /api/connect/github` or `/api/connect/google`
- **Callback Route**: `GET /api/connect/callback?code=...&provider=...`
- **Success**: Redirects to `/dashboard?connected=github` (or google)
- **Database**: Check `Connection` table for stored connections

## ⚠️ Important Notes

- **Descope Setup is Required** for actual GitHub/Google integration
- Without Descope, you can still see the beautiful UI but connections won't work
- The app includes a "Skip for now" option to view the dashboard without connections
- All API keys and tokens are handled securely through Descope's outbound apps

## Features

- Unified dashboard of cross-platform tasks
- AI/heuristic prioritization
- Sync completion status back to source
- Daily digest via email
- Secure authentication via Descope

## Architecture

- **Frontend**: Next.js with TypeScript and Tailwind CSS
- **Backend**: Next.js API routes
- **Database**: SQLite with Prisma
- **Authentication & Outbound**: Descope

## Development

- `npm run build` - Build for production
- `npm run start` - Start production server
- `npm run lint` - Run ESLint

## Deployment

Deploy to Vercel or similar. Set environment variables in the deployment platform. Configure Vercel Cron for `/api/digest`.