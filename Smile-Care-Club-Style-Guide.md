# Markup 
For markup, we use [Jade](http://jade-lang.com/), more specifically [PyJade](https://github.com/syrusakbary/pyjade).

# Styles
# JavaScript
* No trailing semicolons.
* No trailing whitespace.
* Keep lines fewer than 120 characters.
* Always include a space before and after function parameter.
* No console.log statements
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

# Python
* Use underscores, not camelCase, for variable, function and method names (i.e. poll.get_unique_voters(), not poll.getUniqueVoters).

## Django Templating
* Placement of all `block` and `load` tags should be at the top of the file.
* Use underscores for multi-word values.
* Use InitialCaps for class names (or for factory functions that return classes).
* In Django template code, put one (and only one) space between the curly brackets and the tag contents.
```
//bad
{{foo}}

//good
{{ foo }}
```

For more questions regarding Pythonic Coding styles, reference [Django Coding Styles](https://docs.djangoproject.com/en/1.9/internals/contributing/writing-code/coding-style/)
## Template Tags