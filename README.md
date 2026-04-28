# 📸 Evidence Screenshots

This folder stores Postman screenshots captured during the security analysis.
Add your screenshots here following the naming convention below.

## Naming Convention

```
F{number}_{short_description}.png
```

## Required Screenshots per Finding

| File Name | What to Capture |
|-----------|----------------|
| `F01_200ok_no_auth.png` | Postman showing GET /users → 200 OK with no Authorization header |
| `F01_response_body.png` | Full JSON response showing all 10 user records with PII |
| `F02_users_1.png` | GET /users/1 → 200 OK (Leanne Graham) |
| `F02_users_2.png` | GET /users/2 → 200 OK (Ervin Howell) |
| `F02_users_3.png` | GET /users/3 → 200 OK (Clementine Bauch) |
| `F03_response_fields.png` | /users/1 response with geo, company.bs fields visible |
| `F04_no_ratelimit_headers.png` | Response Headers tab showing absence of X-RateLimit-* headers |
| `F05_headers_tab.png` | Response Headers tab with all 5 missing security headers absent |
| `F06_login_token.png` | POST /api/login → token in response |
| `F06_users_no_token.png` | GET /api/users with no Authorization → 200 OK |

## How to Capture in Postman

1. Send the relevant request in Postman
2. For **body evidence**: click the **Body** tab in the response panel → screenshot
3. For **header evidence**: click the **Headers** tab in the response panel → screenshot
4. Use Windows Snip & Sketch (Win+Shift+S) or Mac Screenshot (Cmd+Shift+4) to capture
5. Save the file to this folder with the correct name

## Tips for Professional Evidence

- Always show the full Postman window including the URL bar and status code
- Do not crop out the HTTP status code (200 OK, 401, 429, etc.)
- For header screenshots, scroll to show all headers — especially the *absent* ones
- Annotate missing headers with a red box or arrow using any image editor
