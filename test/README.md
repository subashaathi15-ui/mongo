npm init -y

npm install express mongoose dotenv

npm install -D typescript ts-node-dev @types/node @types/express

npx tsc --init

{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "rootDir": "./src",
    "outDir": "./dist",
    "esModuleInterop": true,
    "strict": true
  }
}

src/
│
├── config/
│   └── db.ts
│
├── models/
│   └── user.model.ts
│
├── controllers/
│   └── user.controller.ts
│
├── routes/
│   └── user.routes.ts
│
├── app.ts
└── server.ts


<!-- src/config/db.ts -->
import mongoose from "mongoose";

export const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI as string);
    console.log("✅ MongoDB Connected");
  } catch (error) {
    console.error("❌ DB Error:", error);
    process.exit(1);
  }
};

<!-- src/models/user.model.ts -->
import { Schema, model, Document } from "mongoose";

export interface IUser extends Document {
  name: string;
  email: string;
  age: number;
}

const userSchema = new Schema<IUser>(
  {
    name: { type: String, required: true },
    email: { type: String, required: true, unique: true },
    age: { type: Number }
  },
  { timestamps: true }
);

export const User = model<IUser>("User", userSchema);



<!-- src/controllers/user.controller.ts -->
import { Request, Response } from "express";
import { User } from "../models/user.model";

export const createUser = async (req: Request, res: Response) => {
  try {
    const user = await User.create(req.body);
    res.status(201).json(user);
  } catch (error) {
    res.status(500).json({ message: "Error creating user", error });
  }
};

export const getUsers = async (_req: Request, res: Response) => {
  const users = await User.find();
  res.json(users);
};

<!-- src/routes/user.routes.ts -->

import { Router } from "express";
import { createUser, getUsers } from "../controllers/user.controller";

const router = Router();

router.post("/", createUser);
router.get("/", getUsers);

export default router;

<!-- src/app.ts -->

import express from "express";
import router from "./routes/user.route";

const app = express();

app.use(express.json());

app.use("/api/users", router);

export default app;


<!-- src/server.ts -->
import dotenv from "dotenv";
import app from "./app";
import { connectDB } from "./config/db";

dotenv.config();

const PORT = process.env.PORT || 5000;

connectDB();

app.listen(PORT, () => {
  console.log(`🚀 Server running on port ${PORT}`);
});

<!-- package.json -->
"scripts": {
  "dev": "ts-node-dev --respawn --transpile-only src/server.ts",
  "build": "tsc",
  "start": "node dist/server.js"
}



username:subashmkq_db_user
password:ZBkACjgcAMv2maBZ