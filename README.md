# website-template

This is a template repo that can be cloned to start a basic website.
There are also some tips on making your site more secure.

And its all free! :D

### GitHub Pages approach

If you clone it to github (as a public repo) and name your repository in the format {username}.github.io it will automatically create a github pages site for you! For example see <a href="https://kevinpgrant.github.io/">https://kevinpgrant.github.io/</a>

### Cloudflare Pages approach

If you prefer to keep your source code repo private (maybe you use some tooling to compile the site from the /src folder into the /dist folder and only want to publish content from /dist) - you can use <a href="https://dash.cloudflare.com/">Cloudflare Pages</a>, and point it at your private github/gitlab repo
     
n.b. Configuring a normal webserver such as Apache or Nginx to serve your website is beyond the scope of this document, sorry! there are plenty of instructions how to do this on the web though :)


## Basic security headers
     
HTTP headers are used to to pass additional information with the body of the HTTP request.
You can use HTTP headers to make guarantees about the content being server, 
to prevent your site from being misused, or the content from being hijacked.

See the documentation for your webserver (if using Apache httpd, Nginx or similar) on how to manage headers.
However if your site is hosted on a static webserver such as Cloudflare, simply add a file called _headers to your project directory.

The syntax for the headers document is

     [url]
       [name]: [value]
 
### For example
 
     /*
       Referrer-Policy: same-origin
       X-Content-Type-Options: nosniff
       X-Frame-Options: DENY
  
This adds the three headers to any HTTP request sent to a URL matching the /* pattern - that is, every URL on your site!
You can read more about the _headers file on the Cloudflare documentation.


### What do they do?

    Referrer-Policy: same-origin
    - Control what information is passed along when a visitor clicks on links in your site. 
      The value same-origin means that, if a visitor to your webpage clicks a link that goes offsite, no information is passed along.
      
    X-Content-Type-Options: nosniff
    - Don’t allow the browser to guess what type of content you are linking,
      requires you properly label the content type, e.g. type="text/css" in any links
      
    X-Frame-Options: DENY
    - prevents your site from being embedded on other websites (misappropriated)

## Content security policy

A CSP (content security policy) is a way for your site to declare what type of content it is serving. This can be used to guarantee that the content on your site is sent exactly as you intended, along with some other features.

For this site, all content is served from the same domain (self), the only non-HTML content is a CSS stylesheet and image (favicon), so our CSP can be pretty restrictive. Here’s what we will use:

Content-Security-Policy:

     default-src 'none'; img-src 'self'; style-src 'self'; upgrade-insecure-requests; form-action 'none'; base-uri 'none'; frame-ancestors 'none'

### Here is an explanation of the various components.

    default-src 'none';
    - by default, we are serving no content other than HTML.
    
    img-src 'self'; style-src 'self';
    - include CSS and images, but only from the same domain.
    
    upgrade-insecure-requests;
    - redirect http:// to https://
    
    form-action 'none';
    - do not send HTTP forms anywhere
    
    base-uri: none;
    the document base URL is just /
    
    frame-ancestors 'none';
    - similar to the 'X-Frame-Options: DENY' from before, since it prevents your site from being embedded elsewhere.

If you want to include JavaScript (or other content), especially content loaded from a different domain, modifying your CSP will require a bit more work.
To see more details on creating a content security policy, see the <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP#writing_a_policy">MDN page</a>.


## HSTS Preload

Essentially, HSTS (HTTP Strict Transport Security) provides a list of sites that are hard-coded to be using HTTPS.
This list is used by your browser to guarantee to prevent connecting to a site on the list without using HTTPS.
If you are committed to using HTTPS, you can register your site for HSTS Preload at the URL https://hstspreload.org.

Make sure you also add a Strict-Transport-Security header as follows:

    Strict-Transport-Security: max-age=31536000; preload; includeSubDomains


## Complete _headers file example for Cloudflare

Here is a _headers file, which implements all of the recommendations above.

     /*
        Content-Security-Policy: default-src 'none'; img-src 'self'; style-src 'self'; upgrade-insecure-requests; form-action 'none'; base-uri 'none'; frame-ancestors 'none'
        Referrer-Policy: same-origin
        Strict-Transport-Security: max-age=31536000; preload; includeSubDomains
        X-Content-Type-Options: nosniff
        X-Frame-Options: DENY

To verify that headers are loaded properly on your site. The <a href="https://observatory.mozilla.org/analyze/cobalt.pages.dev">Mozilla observatory page</a> is a nice resource
