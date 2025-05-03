Follow this instruction when implementing a webapp with auth and paywall. 

- Auth uses Supabase
- Paywall uses Stripe. 
- The paywall is perfered to be implemented in a Cloudflare worker.


# Auth with Supabase

- Provider: Google by default

- Auth workflow should be implemented in the front end code. JWT verfication is in the backend. However, use Supabase's built-in refresh tokens.

- Constant/key names if necessary: SUPABASE_URL, SUPABASE_ANON_KEY, SUPABASE_SERVICE_KEY,
SUPABASE_REDIRECT_URI

Notes:
- The redirect urls must be added in supbase dashboard. Go to your Supabase project  
      dashboard.                   
    • Navigate to "Authentication" -> "URL Configuration".      
    • Under "Redirect URLs", ensure that SUPABASE_REDIRECT_URI is listed exactly there

- How to navigate to Supabase for keys: Go to https://supabase.com/dashboard -> choose Your Project -> Click on Project Settings at the bottom of the left panel -> Click on Authentication under Configuration on the left panel -> Data API

- Sigin payload looks like this:     
            {
                "provider": "google",
                "options": {
                    "redirect_to": SUPABASE_REDIRECT_URI,
                    # "query_params": {
                    #     "prompt": 'select_account'
                    # }
                }
            }
        


# Paywall with Stripe

- Use Stripe for payment, subscriptions paywall.

- Email: should be same as supabase log in email. User should be logged in before subscription.

- Manage user payment and subscription info in Cloudflare D1 table. Key should be supabase user id. Columns should include: 
    userid 
    email
    plan_type 
    payment_status 
    payment_date
    last_payment_attempt  (to track failed payment retries)
    subscription_start_date
    subscription_end_date. 
Feel free to add more columns as needed. Table name: prefixed with App name (defined in .env APP_NAME). App name should be included in .env.

- Keys are saved in .env file. Name the keys: STRIPE_SECRET_KEY, STRIPE_WEBHOOK_SECRET

- Prefer to implement all these in a Cloudflare worker.

# App features

- All app feature logics should be implemented in one separate Cloudflare worker. If you are asked to implement app backend with python, then you use Flask for server, and FastAPI latest versions for api server. But that will be self-host.

- It is advised to not implement product trials. Try to avoid that.

- Frontend: Use JavaScript or HTML, hosted on Cloudflare Pages (preferred) or GitHub Pages.  UI calls serverless functions in the Cloudflare worker/backend to fullfill their features.

- Requests: Include JWT tokens with both authentication and subscription info for verification.

- Best Practices:
    Avoid implementing product trials.
    Ensure modularity by separating frontend and backend logic.


- Error Handling: Implement robust error handling for authentication and payment failures.

- JWT Management: Address token expiration and refresh mechanisms.

- Frameworks: Consider using modern frontend frameworks like React better scalability and maintainability. Simplicity is key though (e.g., vanilla JavaScript or Svelte) might be more appropriate for smaller projects.

## Testing Strategy
- Unit tests: Required for all business logic
- Integration tests: Required for auth and payment flows
- E2E tests: Required for critical user journeys
- Load tests: Required for main API endpoints
- Unit tests Tools: Jest (JavaScript) or Pytest (Python), Playwright. Load tests: k6 or Locust.

## Security Requirements
- Implement rate limiting on all API endpoints. Leverage Cloudflare if possible.
- Use CORS with specific origin validation
- Set secure and httpOnly flags for cookies
- Implement request validation for all inputs
- Handle errors gracefully without exposing system details
- Request validation: zod for JavaScript, pydantic for Python.

## Technology Stack
- Authentication: Supabase with Google OAuth
- Payments: Stripe
- Backend: Cloudflare Workers. Flask or FastAPI should be used (e.g., for more complex backend logic or self-hosted environments).
- Frontend: Cloudflare Pages (primary) or GitHub Pages (alternative)
