
# 🔓 Responsible Disclosure: JWT Token Reuse Post Logout — Zomato.com

## 📌 Summary
A vulnerability was discovered in Zomato’s web application that allowed a user's JWT (JSON Web Token) to be reused even after logging out. This could allow unauthorized access to protected endpoints if the token was intercepted or exposed, violating session integrity principles.

---

## 📋 Description
After logging out from a valid Zomato user session, the JWT token remained valid and continued to authorize sensitive POST and GET requests such as modifying notification preferences. This behavior bypasses proper session termination checks.

While the token is time-bound and eventually expires, its usability post-logout creates a window where an attacker (with access to the token) could impersonate the user.

---

## 🎯 Affected Platform
- **Web application** (https://www.zomato.com)

---

## 🧪 Steps to Reproduce

1. Log in to Zomato with a valid user account.
2. Visit the **Settings > Notification Preferences** section.
3. Intercept and extract the JWT token from an outgoing request (via DevTools or a proxy).
4. Logout from the session.
5. Replay the original request (e.g., modifying preferences) using the same JWT token.

✅ The request is still accepted, and preferences are updated despite the user having logged out.

---

## 🧪 Example Replay (POST Request)

```bash
curl -X POST 'https://api.zomato.com/gw/web/user/notification_preferences' \
  -H 'Content-Type: application/json' \
  -H 'X-Zomato-Csrft: 3501ddaa17faa4c5f79acaea473d8b10' \
  -d '{"token":"<extracted-jwt-token>","origin":"profile","categories":[{"id":1,"email":{"status":true}},{"id":2,"email":{"status":true},"push":{"status":true},"whatsapp":{"status":true}}]}'
```

---

## 📎 Impact

- Exploiting this could allow unauthorized updates to user preferences or other actions.
- Although limited in scope, this indicates a **missing token invalidation** mechanism on logout.
- Could be chained with other vulnerabilities for a more serious impact.

---

## 🛡️ Disclosure Timeline

- 🗓️ **July 20, 2025** – Report submitted via Zomato's responsible disclosure program.
- 📬 **July 21, 2025** – Acknowledged by security team.
- 📝 **July 22, 2025** – Marked as *Informative* and closed.
- 📢 **July 25, 2025** – Publicly disclosed (in line with coordinated disclosure policy).

---

## 📘 Lessons Learned

- Token invalidation on logout is critical to prevent session misuse.
- Developers should consider adding a token revocation list or user-specific logout timestamp validation on the server side.

---

## 🙌 Acknowledgments

Thanks to the Zomato security team for reviewing the report. Though it was closed as informative, this was a great learning opportunity in web app session security.
