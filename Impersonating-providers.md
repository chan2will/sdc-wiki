Go to `http://localhost:8000/provider/cases/?impersonate=[username]`, replacing `[username]` with the username of a provider. For the time being, usernames are email addresses. Example:

```
http://localhost:8000/provider/cases/?impersonate=dr.dowling@daviddowlingortho.com
```

You might be prompted to log in. Use _your_ credentials, not those of the provider you're impersonating. The session is shared with the ecommerce website _and_ the staff portal _and_ the Django backend, so there's a good chance you'll already be logged in.

You can "depersonate" by using the `impersonate` param with no argument, like this:

```
http://localhost:8000/provider/cases/?impersonate
```

Impersonation works in all environments, including production. Be careful.

Not all providers can be impersonated—only those who have a `providers_provider_user` record. To get a list of impersonatable providers, use the following query:

```
SELECT pu.provider_id, pu.user_id, u.username, p.kind, p.corporate_provider, u.role
FROM providers_provider_user pu
LEFT JOIN providers_provider p ON pu.provider_id = p.id
LEFT JOIN profiles_user u ON pu.user_id = u.id
WHERE pu.date_removed IS NULL
```