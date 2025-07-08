# 8. assets/js/dependencies
Allow grunt to load JS files in `index.ejs` before the any others JS file dir.
```
js/
| main.js
| apple.js
| dependencies/
| | sails.io.js
```

This setup will create:

```html
<!--SCRIPTS-->
<script src="/js/dependencies/sails.io.js"></script>
<script src="/js/apple.js"></script>
<script src="/js/main.js"></script>
<!--SCRIPTS END-->
```