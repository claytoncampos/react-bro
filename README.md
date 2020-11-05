reate an Admin in a few minutes
https://adminbro.com

We will play with AdminBro to create our CRUD

Enviroment
We will need

Code Editor
Node.js
Mongo
Terminal
Start with AdminBro
Create structure

mkdir project
cd project
touch admin.js
Install dependencies

npm i admin-bro @admin-bro/express express express-formidable
admin.js

// ============================================
// Admin Bro
const AdminBro = require('admin-bro')
const AdminBroExpress = require('@admin-bro/express')

const adminBro = new AdminBro({
  databases: [],
  rootPath: '/admin',
})

const router = AdminBroExpress.buildRouter(adminBro)

// ============================================
// Server
const express = require("express");
const server = express();

server
  .use(adminBro.options.rootPath, router)
  .listen(5500, () => console.log("Server started"));
Start server

node admin.js
Add Database
install dependencies

npm i @admin-bro/mongoose mongoose
update admin.js

// ============================================
// Database
const mongoose = require("mongoose");

const ProjectSchema = new mongoose.Schema({
  title: {
    type:String,
    required: true,
  },
  description: String,
  completed: Boolean,
  created_at: { type: Date, default: Date.now },
});

const Project = mongoose.model("Project", ProjectSchema);

// ============================================
// Admin Bro
const AdminBro = require('admin-bro')
const AdminBroExpress = require('@admin-bro/express')
const AdminBroMongoose = require('@admin-bro/mongoose')

// use mongoose in AdminBro
AdminBro.registerAdapter(AdminBroMongoose)

// config
const adminBroOptions = new AdminBro({
	resources: [Project],
  rootPath: '/admin'
})
const router = AdminBroExpress.buildRouter(adminBroOptions)


// ============================================
// Server
const express = require("express");
const server = express();

server
  .use(adminBroOptions.options.rootPath, router)

// =============================================
// Run App
const run = async () => {
  await mongoose.connect("mongodb://localhost/adminbroapp", {
      useNewUrlParser: true,
      useUnifiedTopology: true
  });

  await server.listen(5500, () => console.log("Server started"));
}

run()
Customize
https://adminbro.com/tutorial-customizing-resources.html

Change Project menu name and add options to resource
const adminBroOptions = new AdminBro({
	resources: [
    { resource: Project, options: {
      properties: {
        description: { type: 'richtext' },
        created_at: {
          isVisible: { edit: false, list: true, show: true, filter: true }
        }
      }
   }},
  ],
  locale: {
    translations: {
      labels: {
        Project: 'My Projects'
      }
    }
  },
  rootPath: '/admin'
})