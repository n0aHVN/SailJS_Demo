# api/models
This is the directory that holds your models.

In Sails, models are the structures that contain data for your Sails App.

When you call `sails generate api users` via the command line from inside your project's root directory, Sails will generate:
- `api/models/Users.js`
- `api/controllers/Users.js`

## 1. Users.js
### Purpose
- Specify what attributes does 1 model instance (record) have.
- Add custom model instance methods
- Override global settings (Ex: `policies`,`connections`)
- Use [Waterline](https://github.com/balderdashy/waterline) => Model First approach.

