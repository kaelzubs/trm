# Quick Reference: Social Login Integration

## ✅ Verification Checklist

- [x] Google OAuth configured
- [x] Facebook OAuth configured  
- [x] Auto signup enabled
- [x] Custom adapter configured
- [x] Signals registered
- [x] URLs configured
- [x] Templates updated
- [x] 13 tests passing

---

## Access Points

| Feature | URL |
|---------|-----|
| **Email Login** | `/accounts/login/` |
| **Email Register** | `/accounts/register/` |
| **Social Login** | `/accounts/social/` |
| **Google Callback** | `/accounts/google/login/callback/` |
| **Facebook Callback** | `/accounts/facebook/login/callback/` |
| **Logout** | `/accounts/logout/` |
| **Admin** | `/admin/` |

---

## User Journey Examples

### Google Login
1. User clicks "Login with Google"
2. Redirected to Google login
3. Grants permission
4. Returns to app authenticated
5. Auto-created user profile if new

### Facebook Login
1. User clicks "Login with Facebook"
2. Redirected to Facebook login
3. Grants permission (email, profile)
4. Returns to app authenticated
5. Auto-created user profile if new

---

## What Gets Stored

When a user connects a social account:

```python
User Model Fields:
  - username (auto-generated if empty)
  - email (from provider)
  - first_name (from provider)
  - last_name (from provider)

SocialAccount Model:
  - provider (google or facebook)
  - uid (provider's user ID)
  - display_name
  - extra_data (raw provider response)

SocialToken:
  - access_token (for API calls)
  - expires_at
  - refresh_token (if applicable)
```

---

## Environment Variables Needed

Add these to your `.env`:

```
GOOGLE_OAUTH_CLIENT_ID=your_google_client_id
GOOGLE_OAUTH_CLIENT_SECRET=your_google_secret
FACEBOOK_OAUTH_CLIENT_ID=your_facebook_app_id
FACEBOOK_OAUTH_CLIENT_SECRET=your_facebook_secret
```

---

## Files Modified/Created

### Created:
- `accounts/adapters.py` - Custom social account adapter
- `accounts/signals.py` - Signal handlers
- `templates/accounts/social_login.html` - Dedicated social page

### Modified:
- `accounts/views.py` - Added social_login view
- `accounts/urls.py` - Added social_login URL
- `accounts/apps.py` - Register signals
- `shop/settings.py` - OAuth configuration
- `shop/urls.py` - Include allauth URLs

### Tested:
- `accounts/tests.py` - 13 comprehensive tests ✅

---

## Common Tasks

### Customize Auto-Signup Behavior
Edit `shop/settings.py`:
```python
SOCIALACCOUNT_AUTO_SIGNUP = True/False
SOCIALACCOUNT_EMAIL_VERIFICATION = 'optional'  # 'mandatory', 'optional', 'none'
ACCOUNT_EMAIL_REQUIRED = True
```

### Request Additional Scopes
Edit `shop/settings.py` → `SOCIALACCOUNT_PROVIDERS`:
```python
'google': {
    'SCOPE': [
        'profile',
        'email',
        # Add more: 'https://www.googleapis.com/auth/calendar'
    ],
}
```

### Customize User Creation
Edit `accounts/adapters.py` → `populate_user()` method

### Track Login Events
Edit `accounts/signals.py` → Add logging/analytics

---

## Troubleshooting

### Issue: "Google login not available"
**Solution**: Google OAuth credentials missing in `.env` or Django admin

### Issue: User created but without name
**Solution**: Provider not returning `first_name`/`last_name` in scope. Check SCOPE setting.

### Issue: Existing email conflicts
**Solution**: User exists locally without social connection. Link manually in Django admin.

### Issue: Redirect URI mismatch
**Solution**: Ensure callback URL in provider matches: `https://yourdomain.com/accounts/{provider}/login/callback/`

---

## Test Command

```bash
python manage.py test accounts.tests -v 2
```

Expected: 13 tests passing ✅

---

## Security Notes

1. **HTTPS Required**: Always use HTTPS in production
2. **Secret Keys**: Never commit `.env` to version control
3. **Token Storage**: Access tokens are stored (can be disabled in settings)
4. **Email Verification**: Set to 'optional' for faster signup or 'mandatory' for security
5. **CSRF Protection**: Enabled by default in Django

---

## Production Checklist

- [ ] Get real Google OAuth credentials
- [ ] Get real Facebook OAuth credentials
- [ ] Add credentials to production `.env`
- [ ] Set `DEBUG = False`
- [ ] Configure ALLOWED_HOSTS
- [ ] Set up HTTPS/SSL
- [ ] Configure email backend
- [ ] Test full OAuth flow
- [ ] Monitor user signup metrics
