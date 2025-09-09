# ALX Polly: A Polling Application

Welcome to ALX Polly, a full-stack polling application built with Next.js, TypeScript, and Supabase. This project serves as a practical learning ground for modern web development concepts, with a special focus on identifying and fixing common security vulnerabilities.

## About the Application

ALX Polly allows authenticated users to create, share, and vote on polls. It's a simple yet powerful application that demonstrates key features of modern web development:

-   **Authentication**: Secure user sign-up and login.
-   **Poll Management**: Users can create, view, and delete their own polls.
-   **Voting System**: A straightforward system for casting and viewing votes.
-   **User Dashboard**: A personalized space for users to manage their polls.

The application is built with a modern tech stack:

-   **Framework**: [Next.js](https://nextjs.org/) (App Router)
-   **Language**: [TypeScript](https://www.typescriptlang.org/)
-   **Backend & Database**: [Supabase](https://supabase.io/)
-   **UI**: [Tailwind CSS](https://tailwindcss.com/) with [shadcn/ui](https://ui.shadcn.com/)
-   **State Management**: React Server Components and Client Components

---

## 🚀 The Challenge: Security Audit & Remediation

As a developer, writing functional code is only half the battle. Ensuring that the code is secure, robust, and free of vulnerabilities is just as critical. This version of ALX Polly has been intentionally built with several security flaws, providing a real-world scenario for you to practice your security auditing skills.

**Your mission is to act as a security engineer tasked with auditing this codebase.**

### Your Objectives:

1.  **Identify Vulnerabilities**:
    -   Thoroughly review the codebase to find security weaknesses.
    -   Pay close attention to user authentication, data access, and business logic.
    -   Think about how a malicious actor could misuse the application's features.

2.  **Understand the Impact**:
    -   For each vulnerability you find, determine the potential impact.Query your AI assistant about it. What data could be exposed? What unauthorized actions could be performed?

3.  **Propose and Implement Fixes**:
    -   Once a vulnerability is identified, ask your AI assistant to fix it.
    -   Write secure, efficient, and clean code to patch the security holes.
    -   Ensure that your fixes do not break existing functionality for legitimate users.

### Where to Start?

A good security audit involves both static code analysis and dynamic testing. Here’s a suggested approach:

1.  **Familiarize Yourself with the Code**:
    -   Start with `app/lib/actions/` to understand how the application interacts with the database.
    -   Explore the page routes in the `app/(dashboard)/` directory. How is data displayed and managed?
    -   Look for hidden or undocumented features. Are there any pages not linked in the main UI?

2.  **Use Your AI Assistant**:
    -   This is an open-book test. You are encouraged to use AI tools to help you.
    -   Ask your AI assistant to review snippets of code for security issues.
    -   Describe a feature's behavior to your AI and ask it to identify potential attack vectors.
    -   When you find a vulnerability, ask your AI for the best way to patch it.

---

## Security Audit Findings & Remediation

This section documents the security vulnerabilities that were identified and the corrective actions taken to secure the application.

### 1. 🚨 Critical - Broken Access Control in Poll Deletion

-   **Vulnerability**: Any authenticated user could delete any poll in the system, even those they did not create. The `deletePoll` server action only required a valid `poll_id` and did not verify ownership.
-   **Risk**: This allowed for trivial data destruction. A malicious user could wipe out all user-generated content, leading to a total loss of data.
-   **Remediation**: The `deletePoll` function in `app/lib/actions/poll-actions.ts` was modified to enforce ownership. It now retrieves the current user's ID and adds a `.eq("user_id", user.id)` clause to the Supabase query. This ensures the `DELETE` operation only succeeds if the poll's `user_id` matches the ID of the user making the request.

### 2. 🚨 Critical - Unprotected Admin Panel

-   **Vulnerability**: The admin page at `/admin` was a client-side component that fetched and displayed all polls from all users. Access to this page was not restricted on the server.
-   **Risk**: Any authenticated user could navigate to the `/admin` URL and view, manage, and delete every poll in the database. This represents a complete failure of access control for a privileged administrative function.
-   **Remediation**: The admin page was converted from a client component to a server component. Now, before the page is rendered, a server-side check verifies the user's session and queries a `profiles` table to confirm they have an `admin` role. If the user is not an admin, an "Access Denied" message is displayed instead of the sensitive data.

### 3. 🟡 Medium - Unlimited Voting

-   **Vulnerability**: The `submitVote` function did not prevent a user from voting multiple times on the same poll. A user could repeatedly submit votes, illegitimately skewing the poll results.
-   **Risk**: This flaw undermines the integrity of the polling system. Poll outcomes could be easily manipulated, making the application's core functionality untrustworthy.
-   **Remediation**: The `submitVote` function in `app/lib/actions/poll-actions.ts` was updated to include a check for an existing vote. Before inserting a new vote, it now queries the `votes` table to see if a record already exists for the current `user_id` and `poll_id`. If a vote is found, a "You have already voted" error is returned, preventing duplicate votes.

---

## Getting Started

To begin your security audit, you'll need to get the application running on your local machine.

### 1. Prerequisites

-   [Node.js](https://nodejs.org/) (v20.x or higher recommended)
-   [npm](https://www.npmjs.com/) or [yarn](https://yarnpkg.com/)
-   A [Supabase](https://supabase.io/) account (the project is pre-configured, but you may need your own for a clean slate).

### 2. Installation

Clone the repository and install the dependencies:

```bash
git clone <repository-url>
cd alx-polly
npm install
```

### 3. Environment Variables

The project uses Supabase for its backend. An environment file `.env.local` is needed.Use the keys you created during the Supabase setup process.

### 4. Running the Development Server

Start the application in development mode:

```bash
npm run dev
```

The application will be available at `http://localhost:3000`.

Good luck, engineer! This is your chance to step into the shoes of a security professional and make a real impact on the quality and safety of this application. Happy hunting!
