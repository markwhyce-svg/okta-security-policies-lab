# 🔐 Implementing Security Policies in Okta

This lab covers advanced security configurations using **Okta’s policy engine**, including IP zones, dynamic location-based access, MFA enforcement, global session restrictions, and password policy rules.

---

## 🎯 Lab Objectives

- Create trusted and restricted network zones
- Enforce MFA with contextual enrollment policies
- Add authentication rules based on network location
- Apply global session and password policies

---

## 🔹 Lab Activities

---

### 🌐 1. Add an IP Network Zone (Corporate Network)

**Scenario**: Define a trusted zone based on your corporate IP.

**Steps**:
1. Go to **Security > Networks**
2. Click **Add zone > IP Zone**
3. Set **Zone name** to `Corporate Network`
4. Under **Gateway IPs**, click to auto-add your current IP
5. Click **Save**
6. Confirm the zone is active

📸 Screenshot:  
![Corporate Network Zone](screenshots/ip-zone-corporate.png)

---

### 🌍 2. Add a Dynamic Zone for Restricted Countries

**Scenario**: Block authenticator enrollment from restricted regions.

**Steps**:
1. Go to **Security > Networks**
2. Click **Add zone > Dynamic Zone**
3. Set **Zone name** to `Restricted Countries`
4. Under **Locations**, select the countries you want to add
5. Leave **State/Region** empty to block the whole country
6. Click **Save**

📸 Screenshot:  
![Restricted Countries Zone](screenshots/dynamic-zone-restricted.png)

---

### 🔐 3. Configure MFA Enrollment Policy

**Scenario**: Enforce MFA for users not in restricted zones.

**Steps**:

**A. Add Authenticator**
1. Go to **Security > Authenticators**
2. Click **Add authenticator**
3. Select **Security Question**, click **Add**, then click **Recovery**

**B. Create Enrollment Policy**
1. Go to **Enrollment tab**
2. Click **Add a policy**
   - Name: `Employee Enrollment`
   - Description: `Allow enrollment for employees not in a restricted country`
   - Assign to group: `All Employees`
3. Under **Authenticators**:
   - Email: Disabled
   - Okta Verify: Required
   - Password: Required
   - Security Question: Optional
4. Click **Create policy**

**C. Add Rules to Enrollment Policy**
- **Rule 1 – Allow if NOT in Restricted Country**  
  - Name: `Not a Restricted Country`  
  - IF: User’s IP is **Not in zone** → `Restricted Countries`  
  - THEN: Allow enrollment of missing required authenticators

- **Rule 2 – Block if IN Restricted Country**  
  - Name: `Restricted Country`  
  - IF: User’s IP is **In zone** → `Restricted Countries`  
  - THEN: Deny enrollment of all authenticators

📸 Screenshot:  
![MFA Enrollment Policy](screenshots/mfa-enrollment-policy.png)

📸 Screenshot:  
![MFA Rule Logic](screenshots/mfa-policy-rules.png)

---

### 🧑‍💻 4. Add a Rule to Okta Dashboard Authentication Policy

**Scenario**: Allow users on the corporate network to use any 1 factor.

**Steps**:
1. Go to **Security > Authentication Policies**
2. Select **Okta Dashboard policy**
3. Click **Add rule**
   - Name: `Corporate Network Access`
   - IF: User’s IP is **In any of these zones** → select `Corporate Network`
   - THEN:
     - Access is **Allowed after successful authentication**
     - User must authenticate with **Any 1 factor**
     - Prompt for authentication: **Every time**
4. Click **Save**

📸 Screenshot:  
![Dashboard Rule - Corporate Access](screenshots/dashboard-access-rule.png)

---

### 🕒 5. Set Up a Global Session Policy

**Scenario**: To improve security posture, all IT admins must use multifactor authentication to sign in to their
Okta org.

**Steps**:
1. Go to **Security > Global Session Policy**
2. Click **Add policy**
   - Name: `IT Admin Session`
   - Description: `Require MFA for IT Admins`
   - Assign to group: `IT Admins`
3. Click **Create policy**
4. Add a rule:
   - Name: `IT Admin Access`
   - IF: Leave defaults
   - THEN:
     - Access is: **Allowed**
     - MFA is: **Required**
     - Prompt for MFA: **Every sign in**
     - Session timeout: 8 hours
     - Idle timeout: 2 hours
5. Click **Create rule**

📸 Screenshot:  
![Global Session Policy - IT Admins](screenshots/global-session-policy.png)

---

### 🔑 6. Set Up a Password Policy

**Scenario**: To enhance security, employees must sign in to Okta using strong passwords. In addition,
employees need to be able to use account self-service recovery if they aren't in a restricted
country.

**Steps**:

**A. Create the Policy**
1. Go to **Security > Authenticators**
2. Next to **Password**, click **Actions > Edit**
3. Click **Add New Password Policy**
   - Name: `Okta Password Policy - Employees`
   - Applies to: `All Employees`
   - Platform: `Okta`

**B. Configure Settings**
- Min length: 10 characters  
- Complexity: Enable all checkboxes  
- Lockout: 3 failed attempts, unlock after 30 mins

**C. Add Rules**

- **Rule 1 – Allow Recovery (not in restricted country)**  
  - IF: IP **Not in zone** → `Restricted Countries`  
  - THEN:
    - Allow self-service for: Change, Reset, Unlock  
    - Recovery methods: Okta Verify (push), Email, Security Question

- **Rule 2 – Block Recovery (in restricted country)**  
  - IF: IP **In zone** → `Restricted Countries`  
  - THEN:
    - Deny all self-service options

📸 Screenshot:  
![Password Policy Settings](screenshots/password-policy.png)

📸 Screenshot:  
![Password Policy Rules](screenshots/password-policy-rules.png)

---

## ✅ Skills Demonstrated

- 🌍 IP & Geo Zone Configuration
- 🔐 MFA Enrollment Control
- 🔁 Session MFA Rules
- 🔑 Password Policy Enforcement

---

## 📎 Related Labs

- 🧑‍💼 [User Definition & Role Assignment](https://github.com/markwhyce-svg/okta-user-management-lab)
- 👥 [Organizing Users with Groups](https://github.com/markwhyce-svg/okta-organize-users-lab)

---

> 🔧 Built as part of my IAM learning path — by [Michael Akintuyosi](https://www.linkedin.com/in/michael-akintuyosi-025317183)
