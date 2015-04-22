I manage a lot of Django projects that present slightly-different forms to users depending on the site/domain they're visiting.  There's also a bunch of custom submission code that differs from form to form, but that's neither here nor there.

I need different Google Analytics codes depending on the sites and after sticking these tags into every single template, I thought it would be cool to be able to manage these Google analytics accounts from the Django admin page. I also added a mode of operation that excludes the admin interface altogether (you can just use the template tag)

## Aren't there other solutions already? ##

In my searches I ran across two methods that have been around for a while but which I didn't find to be adequate.  The first is a [Google Analytics middleware](http://www.willarson.com/blog/?p=46) by Will Larson.  This method inspects each request, searches it for a `<body>` tag and dynamically insert the google analytics code.

The bad thing is that the template for the tags are hard coded into the middleware, and as far as I know, this kind of defeats the purpose of caching, as a regular expression and some other code must be called for every request.  You've also got to purposefully exclude the admin interface, which most people are never going to want to track anyway.

Finally, you can't tie GA codes to sites (you can specify them in different settings files, though) and have them inserted properly.  I like the ability to administer as much as possible from the admin (when ideal).

The second method I found was [a snippet](http://www.djangosnippets.org/snippets/457/) that defined a simple template tag which replicates the second mode of operation you see below.  It's a little raw, but it works.  The main downside with this is that, again, you cannot edit the codes from the admin interface and you can't tie them to specific sites.

## Two modes of operation ##

### Administering and associating codes with Django sites ###
  1. Add the `google_analytics` application to your `INSTALLED_APPS` section of your `settings.py`.  This mode requires that you be using the [Django sites framework](http://www.djangoproject.com/documentation/sites/) too, so make sure you have that set up as well.
  1. Add `GOOGLE_ANALYTICS_MODEL = True` to your `settings.py`
  1. Run a `./manage.py syncdb` to add the database tables
  1. Go to your project's admin page (usually `/admin/`) and click into a site objects
  1. You'll now see a new field under the normal site information called "Analytics Code". In this box you put your unique analytics code for your project's domain.  It looks like `UA-xxxxxx-x` and save the site.
  1. In your base template (usually a `base.html`) insert this tag at the very top: `{% load analytics %}`
  1. In the same template, insert the following code right before the closing body tag: `{% analytics %}`

### Just using the template tag ###
  1. Add the `google_analytics` application to your `INSTALLED_APPS` section of your `settings.py`.
  1. In your base template, usually a `base.html`, insert this tag at the very top: `{% load analytics %}`
  1. In the same template, insert the following code right before the closing body tag: `{% analytics "UA-xxxxxx-x" %}` the `UA-xxxxxx-x` is a unique Google Analytics code for you domain when you sign up for a new account.