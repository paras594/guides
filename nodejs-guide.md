# Node.js and Express.js Guide

[TOC]

### Folder Structure

-  With template engine

```
- /models
- /routes
- /views
- /public
- /utils
- /sanitization
- /validation
- server.js
```

```bash
touch server.js && \
mkdir client config models controllers routes sanitization validation utils && \
tree
```

-  With frontend framework

```
- /client
- /models
- /config
- /routes
- /sanitization
- /validation
- /utils
- server.js
```

-  New Style

```

```

-  Experimental Structure

Reference: [Link](https://www.coreycleary.me/project-structure-for-an-express-rest-api-when-there-is-no-standard-way/)

```
/controllers
	- users.controller.js
	- index.js
/db
	- users.db.js
	- index.js
/routes
	- users.routes.js
	- index.js
/services
	- users.service.js
	- index.js
/tests
	/controllers
		- users.controller.test.js
	/db
		- users.db.test.js
	/services
		- users.services.test.js
	/utils
		- utils.test.js
/utils
	- index.js

- server.js
```

### Server File

-  For template engine

```bash
#installs
npm i express ejs express-ejs-layouts dotenv
```

```javascript
// server.js [snejsserver]
require("dotenv").config();
const express = require("express");
const ejs = require("ejs");
const expressLayouts = require("express-ejs-layouts");
const path = require("path");
const app = express();
const myRoute = require("./routes/myRoute");

app.use(express.static("./public"));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));

app.set("view engine", "ejs");
app.use(expressLayouts);

app.use("/", myRoutes);

const port = 3000;
app.listen(port, () => console.log(`app started on port: ${port}`));
```

```javascript
// myRoute.js [snroutefile]
const express = require("express");
const router = express.Router();

router.get("/", (req, res) => {
	res.redirect("/");
});

module.exports = router;
```

-  For client api

```bash
#installs
npm i express cors dotenv
```

```javascript
// server.js [snapiserver]
require("dotenv").config();
const express = require("express");
const users = require("./routes/api/users");
const cors = require("cors");

const app = express();

//database connection
require("./config/db");

app.use(express.json());
app.use(express.urlencoded({ extended: false }));

app.use(cors());
app.use(express.static("./public"));

app.use("/api/users", users);

const port = 3000;
app.listen(port, () => console.log(`listening on port: ${port}`));
```

### Route File

```javascript
// routes/route.js
const express = require("express");
const router = express.Router();
const Ninja = require("../models/ninjas");

router.get("/ninjas", (req, res) => {
	Ninja.find({}).then((ninjas) => {
		res.send(ninjas);
	});
	// res.send({ type: 'GET' });
});

router.post("/ninjas", (req, res, next) => {
	Ninja.create(req.body)
		.then((ninja) => {
			res.send(ninja);
		})
		.catch(next);
});

router.put("/ninjas/:id", (req, res, next) => {
	//to update, update with, options -> returns a the obj.
	Ninja.findByIdAndUpdate({ _id: req.params.id }, req.body, {
		useFindAndModify: false,
	}).then(() => {
		Ninja.findOne({ _id: req.params.id }).then((ninja) => res.send(ninja));
	});
});

router.delete("/ninjas/:id", (req, res, next) => {
	// console.log(req.params.id);
	Ninja.findByIdAndRemove({
		_id: req.params.id,
	}).then((ninja) => {
		res.send(ninja);
	});
});

module.exports = router;
```

## Database Connection

### Mongodb setup :

```bash
#installs
npm i mongoose
```

```javascript
// db.js [snmongoconfig]
// require this file in server.js
const dbName = "mern-todo";
const mongoose = require("mongoose");
const mongoURI =
	`mongodb+srv://paras594:password@mern-todo-0pet9.mongodb.net/test?retryWrites=true&w=majority` ||
	`mongodb://localhost:27017/${dbName}`;
const options = {
	useNewUrlParser: true,
	useUnifiedTopology: true,
};

mongoose.connect(mongoURI, options);

mongoose.connection.on("connected", () =>
	console.log("connected on:", mongoURI)
);

mongoose.connection.on("error", (err) => console.log("error:", err));

mongoose.connection.on("disconnected", () => console.log("disconnected"));

process.on("SIGINT", () => {
	mongoose.connection.close(() => {
		console.log("app terminated, closing mongo connection");
		process.exit(0);
	});
});
```

### Mongoose Models/Schema

```javascript
// mongo schema file [snmongoschema]
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const blogsSchema = new Schema({
	title: String, // String is shorthand for {type: String}
	author: String,
	body: String,
	comments: [{ body: String, date: Date }], // arr of objects
	date: { type: Date, default: Date.now },
	hidden: Boolean,
	meta: {
		votes: Number,
		favs: Number,
	},
});

const Blogs = mongoose.model("Blog", blogsSchema);
module.exports = Blogs;
```

### Postgres Setup :

```bash
#installs
npm i pg dotenv
```

```
# .env file [snpgenv]
DB_USER=user
DB_PASSWORD=password
DB_HOST=localhost
DB_PORT=5432
DB_DATABASE=database
```

```javascript
// db.js [snpgconfig]
require("dotenv").config();

const { Pool } = require("pg");
const isProduction = process.env.NODE_ENV === "production";

const connectionString = `postgresql://${process.env.DB_USER}:${process.env.DB_PASSWORD}@${process.env.DB_HOST}:${process.env.DB_PORT}/${process.env.DB_DATABASE}`;

const db = new Pool({
	connectionString: isProduction ? process.env.DATABASE_URL : connectionString,
	ssl: isProduction,
});

module.exports = db; // db.query(...) to query database;
```

## EJS Template Engine

-  Folder Structure

```
- /views
	- /partials
   - layout.ejs
	- index.ejs
```

-  Layout file : [snejslayout]

```ejs
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<meta http-equiv="X-UA-Compatible" content="ie=edge" />
		<link rel="stylesheet" href="style.css" />
		<title>Nodejs Mongodb starter</title>
	</head>
	<body>
		<%- body %>

		<script src="main.js"></script>
	</body>
</html>
```

-  Including partials in a file: [snejsinclude]

```ejs
<% include partials/navbar.ejs %>
<h1>This is a heading</h1>
```

-  Rendering and sending data in ejs file

```javascript
// example 1
app.get("/", (req, res) => {
	res.render("index", { email: "person@gmail.com" });
});

// example 2
app.get("/", function (req, res) {
	const drinks = [
		{ name: "Bloody Mary", drunkness: 3 },
		{ name: "Martini", drunkness: 5 },
		{ name: "Scotch", drunkness: 10 },
	];
	const tagline = "this is a tag line.";

	res.render("index", {
		drinks: drinks,
		tagline: tagline,
	});
});
```

-  Using Data in ejs file

```ejs
<!-- example 1 -->
<h2>Hello <%= email %></h2>

<!-- example 2 -->
<p><%= tagline %></p>

<ul>
    <% drinks.forEach(function(drink) { %>
        <li><%= drink.name %> , <%= drink.drunkness %></li>
    <% }); %>
</ul>
```

## Authentication

### JWT based authentication

```bash
#installs
npm i bcryptjs cors is-empty jsonwebtoken passport passport-jwt validator
```

#### Server setup

```javascript
// server.js
const express = require("express");
const users = require("./routes/api/users");
const passport = require("passport");
const cors = require("cors");
const app = express();

//database connection for mongo
require("./config/db");

app.use(express.json());
app.use(express.urlencoded({ extended: false }));

app.use(cors());
app.use(express.static("./public"));

// passport initialize [sninitpassport]
app.use(passport.initialize());
require("./config/passport")(passport);

app.use("/api/users", users);

const port = 8080;
app.listen(port, () => console.log(`listening on port: ${port}`));
```

#### User model

```javascript
// models/User.js [snuserschema]
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const UserSchema = new Schema({
	name: {
		type: String,
		required: true,
	},
	email: {
		type: String,
		required: true,
	},
	password: {
		type: String,
		required: true,
	},
	date: {
		type: Date,
		default: Date.now,
	},
});

const User = mongoose.model("users", UserSchema);

module.exports = User;
```

#### Passport setup

```javascript
// config/passport.js [snjwtconfig]
const JWTStrategy = require("passport-jwt").Strategy;
const ExtractJwt = require("passport-jwt").ExtractJwt;
const mongoose = require("mongoose");
const User = require("../models/UserSchema");

const opts = {
	jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
	secretOrKey: "secret",
};

module.exports = (passport) => {
	passport.use(
		new JWTStrategy(opts, (jwt_payload, done) => {
			User.findById(jwt_payload.id)
				.then((user) => {
					if (user) return done(null, user);
					return done(null, false);
				})
				.catch((err) => console.log(err));
		})
	);
};
```

#### Sanitization

```js
// sanitizer [snsanitizer]
const Validator = require("validator");

const sanitizeRegisterInput = (data) => {
	// data = { username, email, password, password2 }
	let { username, email, password, password2 } = data;

	username = Validator.trim(username);
	email = Validator.trim(email);
	email = Validator.normalizeEmail(email);

	return { username, email, password, password2 };
};

module.exports = sanitizeRegisterInput;
```

#### Validation

```javascript
// validation/registerValidation.js [snvalidator]
const Validator = require("validator");
const isEmpty = require("is-empty");

const validateRegisterInput = (data) => {
	let errors = {};

	// convert empty fields to an empty string so we can use validator functions
	data.name = !isEmpty(data.name) ? data.name : "";
	data.email = !isEmpty(data.email) ? data.email : "";
	data.password = !isEmpty(data.password) ? data.password : "";
	data.password2 = !isEmpty(data.password2) ? data.password2 : "";

	// name check
	if (Validator.isEmpty(data.name)) {
		errors.name = "name field is required";
	}

	// email checks
	if (Validator.isEmpty(data.email)) {
		errors.email = "email field is required";
	} else if (!Validator.isEmail(data.email)) {
		errors.email = "email is invalid";
	}

	// password checks
	if (Validator.isEmpty(data.password)) {
		errors.password = "password field is required";
	}

	if (Validator.isEmpty(data.password2)) {
		errors.password2 = "confirm password field is required";
	}

	if (!Validator.isLength(data.password, { min: 6, max: 30 })) {
		errors.password = "password must be at least 6 characters";
	}

	if (!Validator.equals(data.password, data.password2)) {
		errors.password2 = "passwords must match";
	}

	return {
		errors,
		isValid: isEmpty(errors),
	};
};

module.exports = validateRegisterInput;
```

#### Routes

```javascript
// routes/api/users.js [snuserroutes]
const express = require("express");
const router = express.Router();
const User = require("../../models/User");
const bcrypt = require("bcryptjs");
const jwt = require("jsonwebtoken");

const validateRegisterInput = require("../../validation/registerValidation.js");
const validateLoginInput = require("../../validation/loginValidation.js");

router.post("/register", (req, res) => {
	const { errors, isValid } = validateRegisterInput(req.body);

	if (!isValid) {
		return res.status(400).json(errors);
	}

	User.findOne({ email: req.body.email }).then((user) => {
		if (user) {
			return res.status(400).json({ message: "email already exists" });
		} else {
			const newUser = new User({
				name: req.body.name,
				email: req.body.email,
				password: req.body.password,
			});

			bcrypt.genSalt(10, (err, salt) => {
				bcrypt.hash(newUser.password, salt, (err, hash) => {
					if (err) throw err;
					newUser.password = hash;
					newUser
						.save()
						.then((user) => res.json(user))
						.catch((err) => console.log(err));
				});
			});
		}
	});
});

router.post("/login", (req, res) => {
	const { errors, isValid } = validateLoginInput(req.body);

	if (!isValid) {
		return res.status(400).json(errors);
	}

	const email = req.body.email;
	const password = req.body.password;

	User.findOne({ email: email }).then((user) => {
		if (!user) {
			return res.status(404).json({ emailNotFound: "email not found" });
		}

		bcrypt.compare(password, user.password).then((isMatch) => {
			if (isMatch) {
				const payload = {
					id: user.id,
					name: user.name,
				};

				jwt.sign(
					payload,
					"secret",
					{
						expiresIn: 31556926,
					},
					(err, token) => {
						res.json({
							success: true,
							token: `Bearer ${token}`,
						});
					}
				);
			} else {
				return res.status(400).json({
					passwordIncorrect: "password incorrect",
				});
			}
		});
	});
});

// authenticating routes;
app.get(
	"/private",
	passport.authenticate("jwt", { session: false, failWithError: true }),
	function (req, res, next) {
		// handle success
		next();
	},
	function (err, req, res, next) {
		// handle err;
		if (err) {
			res.status(err.status).json({
				error: err.message,
			});
		}
	},
	(req, res) => {
		console.log("user:", req.user);
		res.send("this is private route");
	}
);

module.exports = router;
```

### Local Strategy authentication

```bash
#installs
npm i express-session passport passport-local
```

```javascript
// server.js
const express = require("express");
const ejs = require("ejs");
const expressLayouts = require("express-ejs-layouts");
const User = require("./models/UserModel");
const passport = require("passport");
const LocalStrategy = require("passport-local").Strategy;
const session = require("express-session");
const bcrypt = require("bcryptjs");

const app = express();

//database connection
require("./db/db");

app.use(express.json());
app.use(express.urlencoded({ extended: false }));

app.use(express.static("./public"));

app.set("view engine", "ejs");
app.use(expressLayouts);

app.use(
	session({
		secret: "paras",
		saveUninitialized: true,
		resave: true,
	})
);

app.use(passport.initialize());
app.use(passport.session());

passport.use(
	new LocalStrategy({ usernameField: "email" }, (username, password, done) => {
		User.findOne({ email: username }, (err, user) => {
			if (err) {
				return done(err);
			}
			if (!user) {
				return done(null, false);
			}

			bcrypt.compare(password, user.password, (err, result) => {
				if (err) return done(null, false);
				if (!result) return done(null, false);
				if (result) return done(null, user);
			});
		});
	})
);

passport.serializeUser(function (user, done) {
	done(null, user);
});

passport.deserializeUser(function (user, done) {
	done(null, user);
});

function ensureAuthenticated(req, res, next) {
	console.log(req.isAuthenticated());
	if (req.isAuthenticated()) {
		return next();
	}
	res.redirect("/login");
}

app.get("/", (req, res) => {
	res.render("index");
});

app.get("/register", (req, res) => {
	res.render("register", { errors: [] });
});

app.post("/register", (req, res) => {
	const { email, password, password2 } = req.body;

	let errors = [];

	if (password != password2) {
		errors.push("password does not match");
	}

	User.findOne({ email: req.body.email }, (err, user) => {
		console.log(`user: ${user}`);
		if (user) {
			errors.push("user already exist");
			res.render("register", { errors });
		} else {
			const newUser = new User({
				email: req.body.email,
				password: req.body.password,
			});

			bcrypt.genSalt(10, (err, salt) => {
				bcrypt.hash(newUser.password, salt, (err, hash) => {
					newUser.password = hash;
					newUser
						.save()
						.then((user) => res.redirect("/login"))
						.catch((err) => console.log(err));
				});
			});
		}
	});
});

app.get("/login", (req, res) => {
	res.render("login");
});

app.post(
	"/login",
	passport.authenticate("local", {
		failureRedirect: "/login",
	}),
	(req, res) => {
		// console.log(req.body, req.user);
		res.redirect("/profile");
	}
);

app.get("/profile", ensureAuthenticated, (req, res) => {
	console.log(req.isAuthenticated(), req.body, req.user);
	res.render("profile", { email: req.user.email });
});

app.post("/logout", (req, res) => {
	req.logout();
	res.redirect("/");
});

const port = 3000;
app.listen(port, () => console.log(`listening on port: ${port}`));
```

### Multiple auths in one

Reference: [link](https://medium.com/@rustyonrampage/using-oauth-2-0-along-with-jwt-in-node-express-9e0063d911ed)

## Image Upload

```bash
#installs
npm i multer
```

### Disk Storage

```javascript
// uploadImage.js
const multer = require("multer");
const path = require("path");

function sanitizeFile(file, cb) {
	// Define the allowed extension
	let fileExts = [".png", ".jpg", ".jpeg", ".gif"];
	// Check allowed extensions
	let isAllowedExt = fileExts.includes(
		path.extname(file.originalname.toLowerCase())
	);

	// Mime type must be an image
	let isAllowedMimeType = file.mimetype.startsWith("image/");
	if (isAllowedExt && isAllowedMimeType) {
		return cb(null, true); // no errors
	} else {
		// pass error msg to callback, which can be displaye in frontend
		cb("Error: File type not allowed!");
	}
}

const storage = multer.diskStorage({
	destination: path.join(__dirname, "public", "uploads"),
	filename: (req, file, cb) => {
		cb(null, Date.now() + "-" + file.originalname);
	},
});

const upload = multer({
	storage: storage,
	limit: { fileSize: 10000000 }, // 10mb
	fileFilter: (req, file, cb) => {
		sanitizeFile(file, cb);
	},
});

module.exports = upload;
```

```javascript
// usage in routes file
const upload = require("../config/uploadImage.js");
// "imageURL" corresponds to the name of image field in frontend
app.post("/add", upload.single("imageURL"), (req, res) => {
	console.log(req.file);
	const user = new User({
		name: req.body.name,
		imageURL: req.file.path,
	});

	user.save().then((result) => {
		res.status(201).json({
			message: "User registered successfully!",
		});
	});
});
```

### Memory Storage

```javascript
// uploadImage.js
const multer = require("multer");
const path = require("path");

function sanitizeFile(file, cb) {
	// Define the allowed extension
	let fileExts = ["png", "jpg", "jpeg", "gif"];
	// Check allowed extensions
	let isAllowedExt = fileExts.includes(
		file.originalname.split(".")[1].toLowerCase()
	);
	// Mime type must be an image
	let isAllowedMimeType = file.mimetype.startsWith("image/");
	if (isAllowedExt && isAllowedMimeType) {
		return cb(null, true); // no errors
	} else {
		// pass error msg to callback, which can be displaye in frontend
		cb("Error: File type not allowed!");
	}
}

const storage = multer.memoryStorage();

const upload = multer({
	storage: storage,
	limit: { fileSize: 10000000 }, // 10mb
	fileFilter: (req, file, cb) => {
		sanitizeFile(file, cb);
	},
});

module.exports = upload;
```

```javascript
// usage in routes file
const upload = require("../config/uploadImage.js");

// "image" corresponds to the name of image input field in frontend
app.post("/upload", upload.single("image"), (req, res) => {
	// create img str [snimgstr]
	const base64str = req.file.buffer.toString("base64");
	const imageStr = `data:${req.file.mimetype};base64,${base64str}`;

	db.query(
		"INSERT INTO myimages (image) VALUES ($1)",
		[imageStr],
		(err, result) => {
			if (err) {
				console.log("error occured", err);
				return;
			}
			console.log("data inserted successfully");
			res.json("success");
		}
	);
});
```

## Email Systems

### Using gmail SMTP

```bash
# installs
npm i nodemailer
```

```js
// config/mailTransporter.js [snmailconfig]
const nodemailer = require("nodemailer");

// enable less secure apps for the mail
const gmailAddress = "your gmail address @ gmail.com";
const gmailPass = "your gmail password";

let transporter = nodemailer.createTransport({
	service: "gmail",
	auth: {
		user: gmailAddress,
		pass: gmailPass,
	},
});

module.exports = transporter;
```

```js
// routes
const transporter = require("../config/mailTransporter.js");

router.post("/contact", async (req, res) => {
	try {
		// send mail [snsendmail]
		const info = await transporter.sendMail({
			from: "sender",
			to: "receiver's mail",
			subject: "sending email with nodejs",
			text: "this is a message",
		});

		console.log(info.response);
	} catch (err) {
		console.log(err);
	}
});
```

### Mailgun service

```bash
# installs
npm i nodemailer nodemailer-mailgun-transport
```

```
# .env file [snmailgunenv]
API_KEY=mailgun_api_key
DOMAIN=mailgun_domain
```

```js
// config/mailTransporter.js [snmailgunconfig]
const nodemailer = require("nodemailer");
const mailGun = require("nodemailer-mailgun-transport");

const auth = {
	auth: {
		api_key: process.env.API_KEY,
		domain: process.env.DOMAIN,
	},
};

const transporter = nodemailer.createTransport(mailGun(auth));

module.exports = transporter;
```

```js
// routes
const transporter = require("../config/mailTransporter.js");

router.post("/contact", async (req, res) => {
	try {
		const info = await transporter.sendMail({
			from: "sender",
			to: "receiver's mail",
			subject: "sending email with nodejs",
			text: "this is a message",
		});

		console.log(info.response);
	} catch (err) {
		console.log(err);
	}
});
```

## Forgot Password Setup

```js
// routes [snforgotpassroute]
router.post("/forgot-password", async (req, res) => {
	// get the email to send reset link to
	const { email } = req.body;

	try {
		// check if email exists
		const user = await getUserByEmail(email);

		if (!user) {
			return res.status(400).json({
				success: false,
				message: "Request failed.",
				errors: {
					error: "Email not found.",
				},
			});
		}

		// create reset id and request
		const uniqueId = uuid();
		const request = {
			id: uniqueId,
			email,
		};

		// save the request in database
		await createResetQuery(request);

		// send mail
		const info = await transporter.sendMail({
			from: "Bnf App",
			to: email,
			subject: "Reset Bnf App password",
			text: `To reset your password, click on the following link: http://frontend-react-route/reset/${uniqueId}`,
		});

		console.log(info.response);

		return res.json({
			success: true,
			message: "Email sent.",
		});
	} catch (err) {
		res.status(400).json({
			success: false,
			message: "Request failed.",
			errors: {
				error: err,
			},
		});
	}
});

router.patch("/reset", async (req, res) => {
	// get the unique reset id and new password
	const { id, password } = req.body;

	try {
		// check if request exists
		const resetRequest = await getResetQueryById(id);

		if (!resetRequest) {
			return res.status(400).json({
				success: false,
				message: "Request failed.",
				errors: {
					error: "Reset query not found.",
				},
			});
		}

		// check for expiry of reset query
		const expiryDate = moment(resetRequest.date).add(1, "days");
		const isValid = moment().isBefore(expiryDate);

		if (!isValid) {
			// delete the query
			await deleteResetQueryById(id);

			return res.status(400).json({
				success: false,
				message: "Request Timeout.",
				errors: {
					error: "Your reset password period has expired. Make a new reset password request.",
				},
			});
		}

		// reset password
		const user = await getUserByEmail(resetRequest.email);

		// hash password and update it in db
		bcrypt.genSalt(10, (err, salt) => {
			bcrypt.hash(password, salt, async (err, hash) => {
				if (err) {
					return res.status(400).json({
						success: false,
						message: "Some error occured.",
						errors: {
							error: err,
						},
					});
				}

				const hashedPassword = hash;

				await updateUserPasswordById(user.user_id, hashedPassword);

				return res.status(200).json({
					success: true,
					message: "Password updated.",
				});
			});
		});
	} catch (err) {
		res.json({
			success: false,
			message: "Request failed.",
			errors: {
				error: err,
			},
		});
	}
});
```
