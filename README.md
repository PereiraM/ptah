ptah
====

A python static site generator, based on Flask, Flask-Frozen, and Flask-FlatPages

Attributions
------------
Original inspiration by Nicolas Perriault, on his [blog](https://nicolas.perriault.net/code/2012/dead-easy-yet-powerful-static-website-generator-with-flask/)

Requirements
------------
  - python
  - Flask
  - Flask-FlatPages
  - Flask-Assets
  - Frozen-Flask
  - jsmin
  - cssmin
  - django-htmlmin

Using
-----

  1. Install all the requirements on your client
    `pip install Flask Flask-FlatPages Flask-Assets Frozen-Flask jsmin cssmin django-htmlmin`
  2. Clone this repo
    `git clone http://github.com/gatesphere/ptah`
  3. Customize - see below.
  4. Preview
    `python sitebuilder.py`
  5. Publish
    `python sitebuilder.py build`

License
-------
Murky.  But I'd say use it for personal use?  I'm not sure what Nicolas 
Perriault's license on his code is.

Customizing
-----------
Ptah is really simple to customize - most work is done in the templates.

Here are a few more advanced additions you can do by playing around with
`sitebuilder.py` though:

### Generate error pages
Assuming you're deploying as static html, and you have your webserver
set up for it, you can generate some error pages using your site's 
layout.

In `templates/error.html`:
    
    {% extends "base.html" %}
    {% block content %}
      <h1>error {{ error }}</h1>
      <p>Sorry.  Would you like to go back <a href="{{ url_for('index') }}">home</a>?</p>
    {% endblock content %}
    
In `sitebuilder.py` above `if __name__ == '__main__':`:

    @app.route('/error/error-<int:error>.html')
    def error(error):
      return html_minify(render_template('error.html', error=error))

    @freezer.register_generator
    def error_generator():
      return [
        ('error', {'error' : '400' }),
        ('error', {'error' : '401' }),
        ('error', {'error' : '403' }),
        ('error', {'error' : '404' }),
        ('error', {'error' : '410' }),
        ('error', {'error' : '500' }),
        ('error', {'error' : '501' }),
        ('error', {'error' : '502' }),
        ('error', {'error' : '503' })
      ]

When you do a build, you'll get some formatted error pages in 
`build/error/error-<code>.html`.

### Insert placeholder pages for missing content on build:
If you'd like to insert some placeholder pages for pages you've linked
to but not defined yet, you can add the following to `sitebuilder.py`
before `if __name__ == '__main__':`:

    @app.errorhandler(404)
    def page_not_found(e):
      return html_minify(render_template('placeholder.html'))
      
And then this in `templates/placeholder.html`:

    {% extends "base.html" %}
    {% block content %}
    <h1>placeholder</h1>
    <p>This page hasn't been created yet.</p>
    {% endblock content %}

And when you do a build, it will render a placeholder page wherever
your real page will be when you get around to it.
