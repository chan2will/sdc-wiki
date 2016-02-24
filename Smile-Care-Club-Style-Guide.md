# Markup 
For markup, we use [Jade](http://jade-lang.com/), more specifically [PyJade](https://github.com/syrusakbary/pyjade).

# Styles
# JavaScript
* No trailing semicolons.
* No trailing whitespace.
* Keep lines fewer than 120 characters.
* Always include a space before and after function parameter.
* No console.log statements

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
`}`

# Django Templating
## Template Tags