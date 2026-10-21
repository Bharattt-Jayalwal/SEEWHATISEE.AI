# SeeWhatISee AI

A responsive visual problem-solving app for everyday, low-risk questions about objects, setup, learning, and troubleshooting. It has photo upload, a permission-led camera workspace, step-by-step visual callouts, optional spoken narration, and opt-in live camera follow-up.

## Requirements

- Node.js 20.9 or newer
- pnpm 9 or newer

## Run locally

```bash
pnpm install
pnpm dev
```

Open `http://localhost:3000`. Camera access requires HTTPS in deployed environments; browsers generally allow it on `localhost` for development.

## Demo mode and live analysis

Without an API key the app runs in **Demo mode**. The sample response is static and explicitly says it did not inspect the selected image. Demo requests send text only; frames stay in the browser.

To enable real visual analysis:

1. Copy `.env.example` to `.env.local`.
2. Add a server-side `OPENAI_API_KEY`.
3. Optionally set `OPENAI_MODEL` (the default is `gpt-4.1-mini`).
4. Restart the development server.

The key is read only by the Node.js route handler. The handler accepts a JPEG, PNG, or WebP data URL, checks the bytes against the declared format, limits frame and context size, applies an in-process request limit, and sends one frame per request to the OpenAI Responses API. `store: false` is set on provider requests. Live guide sends one still frame every eight seconds only after a user starts it, and stops when paused, when the camera stops, or when the session ends. Provider retention and training policies are separate from this app; review them before any public launch.

## Data flow and privacy

- The camera starts only after a user presses **Start camera** and every media track is stopped on camera stop, session reset, or unmount.
- The app analyzes a captured or uploaded frame after the user submits a question. Live guide is opt-in and sends one camera frame every eight seconds while enabled; pause or stop it at any time.
- Images and conversation are held in the current tab. The app does not persist them to a database or log request bodies.
- Live mode sends each requested still frame, current task, user-confirmed progress, and a bounded recent chat history to the configured AI provider.
- Demo mode sends no image. It is suitable for demonstrating the interface, not for visual identification.
- AI suggestions never set a task step to complete; the user explicitly confirms each physical step.

Read the in-product [privacy and data-flow page](/privacy) for the user-facing explanation.

## Development commands

```bash
pnpm dev        # local development server
pnpm lint       # ESLint checks
pnpm typecheck  # strict TypeScript check
pnpm test       # Vitest unit tests
pnpm build      # production build
pnpm start      # serve the production build
```

## Deployment

Deploy as a Node.js application on Vercel or another compatible host. Provide `OPENAI_API_KEY` and optionally `OPENAI_MODEL` as server-side environment variables. Set `NEXT_PUBLIC_CONTACT_EMAIL` only if a real contact address is ready to publish. Camera use requires HTTPS in production. The route’s in-memory rate limit is best-effort and resets when a server instance restarts; use a shared rate-limit service before a multi-instance public launch.

## Supported browsers and limitations

The UI targets current desktop and mobile Chrome, Edge, Firefox, and Safari. Camera support requires `getUserMedia` and a secure context. File upload is a fallback. The hero’s 3D lens has a static visual fallback when WebGL is unavailable, and it respects reduced-motion settings. Live guide analyzes periodic still frames rather than streaming continuous video or generating a rendered video file. Its walkthrough advances through numbered steps, adds an image callout only when the target is clear, and can read steps aloud in supported browsers. The product does not provide persistent accounts or calibrated confidence scores.

No API credentials are included in the repository. Provider calls have not been verified against an account unless a key is configured.

