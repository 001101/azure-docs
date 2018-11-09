---
title: User flow versions in Azure Active Directory B2C | Microsoft Docs
description: Learn about the versions of user flows available in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman

ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: davidmu
ms.component: B2C
---

# User flow versions

User flows help you to set up common [policies](active-directory-b2c-reference-policies.md) that fully describe customer identity experiences such as sign-up, sign-in, or profile editing. In Azure Active Directory B2C, you have the option to select from a collection of both recommended user flows and preview user flows. 

New user flows will be added as new versions. As the user flows become stable, they will be recommended for use. You should always use a recommended user flow for any production application, but can freely choose from other versions to test new functionality as it becomes avaliable.

## V1

| User flow | Recommended | Description |
| --------- | ----------- | ----------- |
| Password reset | Yes | Enables a user to choose a new password after verifying their email. Using this user flow, you can configure: <ul><li>[multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>Token compatibility settings</li><li>[password complexity requirements](active-directory-b2c-reference-password-complexity.md)</li> |
| Profile editing | Yes | Enables a user to configure their user attributes. Using this user flow, you can configure: <li>[Token lifetime](active-directory-b2c-reference-tokens.md)</li><li>Token compatibility settings</li><li>Session behavior</li> |
| Resource owner | No | Enables a user with a local account to sign in directly in native applications (no browser required). Using this user flow, you can configure: <li>[Token lifetime](active-directory-b2c-reference-tokens.md)</li><li>Token compatibility settings</li> |
| Sign in | No | Enables a user to sign in to their account. Using this user flow, you can configure: <ul><li>[multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Token lifetime](active-directory-b2c-reference-tokens.md)</li><li>Token compatibility settings</li><li>Session behavior</li> |
| Sign up | No | Enables a user to create an account. Using this user flow, you can configure: <ul><li>[multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Token lifetime](active-directory-b2c-reference-tokens.md)</li><li>Token compatibility settings</li><li>Session behavior</li><li>[password complexity requirements](active-directory-b2c-reference-password-complexity.md)</li> |
| Sign up and sign in | Yes | Enables a user to create an account or sign in to their account. Using this user flow, you can configure: <ul><li>[multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Token lifetime](active-directory-b2c-reference-tokens.md)</li><li>Token compatibility settings</li><li>Session behavior</li><li>[password complexity requirements](active-directory-b2c-reference-password-complexity.md)</li> |

## V2

| User flow | Recommended | Description |
| --------- | ----------- | ----------- |
| Password reset v2 | No | Enables a user to choose a new password after verifying their email. Using this user flow, you can configure: <ul><li>[multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>Token compatibility settings</li><li>[age gating](basic-age-gating.md)</li><li>[password complexity requirements](active-directory-b2c-reference-password-complexity.md)</li>|
| Sign in v2 | No | Enables a user to sign in to their account. Using this user flow, you can configure: <ul><li>[multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Token lifetime](active-directory-b2c-reference-tokens.md)</li><li>Token compatibility settings</li><li>Session behavior</li><li>[age gating](basic-age-gating.md)</li><li>Sign-in page customization</li> |
| Sign up v2 | No | Enables a user to create an account. Using this user flow, you can configure: <ul><li>[multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[Token lifetime](active-directory-b2c-reference-tokens.md)</li><li>Token compatibility settings</li><li>Session behavior</li><li>[age gating](basic-age-gating.md)</li><li>[password complexity requirements](active-directory-b2c-reference-password-complexity.md)</li>|
| Sign up and sign in v2 | No | Enables a user to create an account or sign in their account. Using this user flow, it is possible to enable: <ul><li>[multi-factor authentication](active-directory-b2c-reference-mfa.md)</li><li>[age gating](basic-age-gating.md)</li><li>[password complexity requirements](active-directory-b2c-reference-password-complexity.md)</li> |
