# Create controllers in SailJS in `/api/controller`
**Name Convention:**

To create a controller Javascript file, it must ends with `Controller.js` (For example, `UserController.js`, `MyController.js`)

Example:
```js
module.exports = {
  hi: function (req, res) {
    return res.send('Hi there!');
  },
  bye: function (req, res) {
    return res.redirect('http://www.sayonara.com');
  }
};
```

## Thin Controller
This is a **FAT controller**, because controller should handle request only:
```js
// api/controllers/ProductController.js
module.exports = {
  create: async function (req, res) {
    const { name, price } = req.body;
    if (!name || !price) {
      return res.badRequest('Missing parameters');
    }

    const product = await Product.create({ name, price }).fetch();
    return res.json(product);
  }
};
```


=> Solution: Use `service` to minimize the controller.
```js
// api/controllers/ProductController.js
module.exports = {
  create: async function (req, res) {
    try {
      const product = await ProductService.createProduct(req.body);
      return res.json(product);
    } catch (err) {
      return res.serverError(err.message);
    }
  }
};
```

```js
// api/services/ProductService.js
module.exports = {
  createProduct: async function (data) {
    const { name, price } = data;
    if (!name || !price) {
      throw new Error('Missing parameters');
    }

    return await Product.create({ name, price }).fetch();
  }
};

```