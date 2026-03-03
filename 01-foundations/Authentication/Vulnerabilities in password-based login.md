## Vulnerabilities in password-based login

-**Authentication** is the process of verifying that a user is who they claim to be. Authorization involves verifying whether a user is allowed to do something.

### How authentication vulnerabilities arise

> 1. The authentication mechanisms are weak because they fail to adequately protect against brute-force attacks.
> 2. Logic flaws or poor coding in the implementation allow the authentication mechanisms to be bypassed entirely by an attacker. This is sometimes called "broken authentication".

### 1.Brute-force attacks

-A brute-force attack is when an attacker uses a system of trial and error to guess valid user credentials.
-This can be a chieved using wordlists of usernames and passwords

**During recon check for :**
1.Weather the website siscloses potential usernames publicly
_For example, are you able to access user profiles without logging in? Even if the actual content of the profiles is hidden, the name used in the profile is sometimes the same as the login username_
_You should also check HTTP responses to see if any email addresses are disclosed. Occasionally, responses contain email addresses of high-privileged users, such as administrators or IT support_

#### Brute-forcing passwords

> Passwords can similarly be brute-forced, with the difficulty varying based on the strength of the password

#### Username enumeration

> Username enumeration is when an attacker is able to observe changes in the website's behavior in order to identify whether a given username is valid.

> It tipically occurs on login forms
> _example, when you enter a valid username but an incorrect password, or on registration forms when you enter a username that is already taken_

>While atemting to brute-force a login page try to pay particular attention to any defferences in:
1. **Status codes**: During a brute-force attack, the returned HTTP status code is likely to be the same for the vast majority of guesses because most of them will be wrong. If a guess returns a different status code, this is a strong indication that the username was correct. It is best practice for websites to always return the same status code regardless of the outcome, but this practice is not always followed.
2. Error messeges: Sometimes the returned error message is different depending on whether both the username AND password are incorrect or only the password was incorrect. It is best practice for websites to use identical, generic messages in both cases, but small typing errors sometimes creep in. Just one character out of place makes the two messages distinct, even in cases where the character is not visible on the rendered page.
3. Response times: f most of the requests were handled with a similar response time, any that deviate from this suggest that something different was happening behind the scenes. This is another indication that the guessed username might be correct *For example, a website might only check whether the password is correct if the username is valid. This extra step might cause a slight increase in the response time. This may be subtle, but an attacker can make this delay more obvious by entering an excessively long password that the website takes noticeably longer to handle.*

### Portswiger Lab: Username enumeration via different responses

> Some wesites accept more values in their log in forms in jwt webs by inserting an array of values