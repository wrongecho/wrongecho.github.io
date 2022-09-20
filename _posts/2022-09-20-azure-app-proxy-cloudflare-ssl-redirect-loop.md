## Azure App Proxy Redirect Loop when using Cloudflare

Ran into an interesting issue today when setting up an Azure App Proxy with Cloudflare.

After configuring the App Proxy to use a dedicated custom domain, and setting the appropriate CNAME record in Cloudflare, Azure of course prompts for an SSL certificate.
Being a cheapskate, I wanted to use Cloudflare's Free SSL/TLS certificate offering. You install their self-signed origin certificate on Azure, they proxy the traffic and deliver the final leg of traffic to the user encrypted with Let's Encrypt - easy, right?

Not quite.

Upon loading the custom hostname in the browser, it would just **constantly redirect back to itself over and over**. Checking the headers, I could see it was hitting the app proxy and accepting the cookies being offered, but just redirecting over and over.

**It turned out that [Cloudflare's encryption mode](https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/) needed to be set to Full / Full (strict)**.

It turns out that the default "Flexible" option sends the requests from Cloudflare to the origin server *unencrypted*. The app proxy ("origin server") redirects everything to HTTPS and causes Cloudflare to loop. Had this not been a new domain, I'd have probably already changed the encryption mode to full, but worth documenting here as we all forget & make mistakes sometimes.
