# Design a URL Shortening Service

Design something like bit.ly. A service where anyone can enter a URL, get a shorter URL to use in its place, and we manage redirecting them.

## Q&A

??? question "What sort of scale are we talking about?"

    Millions of redirects every day, and we dont want to make any design decision that migh limit us later, so assume millions of URLs as well.

??? question "Any restrictions on the characters we use? Symbols might be a little too hard for people to remember or type."

     