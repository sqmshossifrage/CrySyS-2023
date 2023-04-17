This was meant to be an easy challenge, but I bashed my head against the wall for three nights on it because for some reason I did not understand the flag in my session was removed. It was only restored the third night I attempted it. 

The webpage is an account management system for a bank. Register for an account and realize that it has a place to store a secret for a user. The secret will then show up on the login screen and the input is vulnerable to stored XSS. 

Then use Burp to discover that the field for changing password is not authenticated and any user can change the password for anyone. Then we just change everyone's password by running through the account number from 1 to 60. It seems the person mentioned in the challenge description has account number 14. Logging into his account and the flag is his stored secret. 
