# General
* No print statements, console.log statements or commented code in PRs.
* Delete extra whitespace. 

# Python
* Use underscores, not camelCase, for variable, function and method names (i.e. `poll.get_unique_voters()`, not `poll.getUniqueVoters`).

## Django Templating
* Placement of all `block` and `load` tags should be at the top of the file.
* Use underscores for multi-word values.

* In Django template code, put one (and only one) space between the curly brackets and the tag contents.
```
//bad
{{foo}}

//good
{{ foo }}
```

For more questions regarding Pythonic Coding styles, reference [Django Coding Styles](https://docs.djangoproject.com/en/1.9/internals/contributing/writing-code/coding-style/) or [Python Style Guide](https://www.python.org/dev/peps/pep-0008/)
## Template Tags

# Markup
## HTML/Jade 
For markup, we use [Jade](http://jade-lang.com/), more specifically [PyJade](https://github.com/syrusakbary/pyjade).

## Styles
* Don't use IDs, use Classes.
* Don't use vendor prefixes, because we use [Auto Prefixer](https://css-tricks.com/autoprefixer/)
* Only global styles belong in app.scss
* All colors belong in _app.variables.scss. If you need to add a new color, add a hex (not rgb) color in this file.

#### Sizing
* Use "ems" for font-sizing
* Use "rems" for margin and padding
* Only use pixels for image sizing
* [Here](https://j.eremy.net/confused-about-rem-and-em/) is a great description on differences.

[CSS Tricks](https://css-tricks.com/) is a great reference for CSS rule-of-thumbs.

# JavaScript
* No trailing semicolons.
* No trailing whitespace.
* Keep lines fewer than 120 characters.
* Always include a space before and after function parameter.
* Cache jQuery selectors

```
// bad
function setSidebar() {
  $('.sidebar').hide();

  // ...stuff...

  $('.sidebar').css({
    'background-color': 'pink'
  });
}

// good
function setSidebar() {
  var $sidebar = $('.sidebar');
  $sidebar.hide();

  // ...stuff...

  $sidebar.css({
    'background-color': 'pink'
  });
}
```