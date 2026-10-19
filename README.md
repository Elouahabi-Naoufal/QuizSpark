<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a id="readme-top"></a>

<!-- PROJECT SHIELDS -->
<!--
*** I'm using markdown "reference style" links for readability.
*** Reference links are enclosed in brackets [ ] instead of parentheses ( ).
*** See the bottom of this document for the declaration of the reference variables.
*** https://www.markdownguide.org/basic-syntax/#reference-style-links
-->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

<!-- PROJECT TITLE BLOCK -->
<br />
<div align="center">
  <a href="https://github.com/Elouahabi-Naoufal/smarter2">
    <h3 align="center">smarter2</h3>
  </a>

  <p align="center">
    Quiz Spark — a self-hosted quiz builder and player built with Next.js and MongoDB.
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/smarter2/blob/main/app/api/quizzes/route.js"><strong>API routes »</strong></a>
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/smarter2/issues">Report Bug</a>
    &middot;
    <a href="https://github.com/Elouahabi-Naoufal/smarter2/issues">Request Feature</a>
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#how-it-works">How it works</a></li>
        <li><a href="#repository-layout">Repository layout</a></li>
        <li><a href="#data-model">Data model</a></li>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->
## About The Project

Quiz Spark is a browser-based quiz application: build a multiple-choice quiz, play it
under a per-question timer, and get a score with a running experience total. It is a
Next.js 14 App Router project in plain JavaScript, with a MongoDB database reached
through Mongoose over Next.js Route Handlers.

The package is named `quiz-spark` in `package.json`, and the title rendered by
`app/layout.js` is "Quiz Spark".

### How it works

**State lives in one client context.** `app/ContextApi.js` creates a single
`GlobalContext` and puts the whole application state in it: the quiz list, the selected
quiz, the quiz queued to start, the user, the chosen icon, dropdown state, loading flag
and `userXP`. Components read it with the exported `useGlobalContextProvider` hook.
The provider is mounted once in `app/layout.js`.

**Quizzes are fetched on mount.** On first render the provider calls
`GET /api/quizzes` and stores the result in `allQuizzes`. It also calls `POST /api/user`
with a hardcoded `quizUser` profile; the route returns the existing user if any document
exists, otherwise it creates one.

**The builder** (`app/quiz-build/page.jsx`) lets you pick a Font Awesome icon, type a
title, and add questions. Each question has a textarea, four choices labelled `A.`
through `D.`, and a single-character correct-answer field. Adding a question is guarded:
if the previous question is empty, or a choice is blank, or no correct answer is set, a
`react-hot-toast` error appears instead. Saving validates everything once more, then
either `POST`s a new quiz or `PUT`s the existing one, and navigates back to `/`.

**The player** (`app/quiz-start/page.jsx`) gives each question 30 seconds
(`const time = 30` in `app/Components/QuizStartPage/QuizStartQuestions.js`). Selecting
an option and pressing Submit grades it: correct answers increment the score, award one
experience point (persisted with `PUT /api/user`) and advance after two seconds;
incorrect answers are counted as such and advance after 1.2 seconds. Letting the timer
run out counts as an incorrect attempt and moves on after a second. At the end a panel
shows `score / total`, the correct/incorrect split, a result emoji chosen from the
percentage, and a Try Again button.

**Statistics are written back.** Every graded question increments `totalAttempts` plus
either `correctAttempts` or `incorrectAttempts`, and the updated questions are saved
with `PUT /api/quizzes?id=...` when the quiz ends. `answeredResult` is reset to `-1`
afterwards so a replay starts clean.

### Repository layout

```
smarter2/
├── app/
│   ├── api/
│   │   ├── quizzes/route.js      # GET, POST, PUT, DELETE
│   │   └── user/route.js         # POST, PUT
│   ├── Components/
│   │   ├── QuizBuildPage/        # builder: title, nav, questions, choices, icons
│   │   ├── QuizStartPage/        # header + question runner and score panel
│   │   ├── DropDown.js, Navbar.js, PlaceHolder.js
│   │   ├── QuizCard.js, QuizzesArea.js, ScoreComponent.js
│   ├── models/
│   │   ├── QuizSchema.js
│   │   └── UserSchema.js
│   ├── page.js                   # /         — currently a scratch page
│   ├── quiz-build/page.jsx       # /quiz-build
│   ├── quiz-start/page.jsx       # /quiz-start
│   ├── ContextApi.js             # global client state
│   ├── QuizzesData.js            # hardcoded seed quizzes (currently unused)
│   ├── iconsData.js, convertToFaIcons.js, convertFromFaToText.js
│   ├── layout.js, globals.css
├── libs/mongoDB.js               # Mongoose connection helper
├── public/                       # images and SVGs
├── next.config.mjs, jsconfig.json, tailwind.config.js, postcss.config.js
└── package.json
```

### Data model

Two Mongoose schemas in `app/models/`.

`QuizSchema` — an `icon` string, a `quizTitle`, and an array of questions:

```js
{
  id:            String,  // uuid v4, generated in the builder
  mainQuestion:  String,
  choices:       [String],
  correctAnswer: Number,  // index into choices
  answeredResult: Number, // defaults to -1, i.e. unanswered
  statistics: {
    totalAttempts:   Number,
    correctAttempts: Number,
    incorrectAttempts: Number,
  },
}
```

`UserSchema` — `name`, `isLogged` and `experience`, all required.

The `icon` is stored as text rather than as the Font Awesome component itself, because
React components do not survive serialisation to JSON. `app/convertFromFaToText.js` and
`app/convertToFaIcons.js` convert in both directions at the component boundary.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Built With

* [![Next][Next.js]][Next-url]
* [![React][React.js]][React-url]
* [![MongoDB][MongoDB.url]][MongoDB]
* [![Mongoose][Mongoose.url]][Mongoose]
* [![TailwindCSS][TailwindCSS.url]][TailwindCSS-url]
* [![FontAwesome][FontAwesome.url]][FontAwesome-url]

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

* **Node.js 18.17 or newer** — required by Next.js 14.
* **npm** (a `package-lock.json` is committed, so npm is the expected package manager).
* **A MongoDB instance** — a local `mongod`, or an Atlas cluster. The connection string
  is read from the `MONGO_URL` environment variable in `libs/mongoDB.js:6`.

### Installation

1. Clone the repo

   ```sh
   git clone https://github.com/Elouahabi-Naoufal/smarter2.git
   cd smarter2
   ```

2. Install dependencies

   ```sh
   npm install
   ```

3. Configure the environment

   `MONGO_URL` is the only variable the code reads (`libs/mongoDB.js:6`). Create
   `.env.local` in the project root:

   ```sh
   MONGO_URL=mongodb://127.0.0.1:27017/quiz-spark
   ```

   Next.js loads `.env` and `.env.local` automatically, so no export step is needed.

   Note that a `.env` file is committed at the repository root, and `.gitignore`
   excludes `.env*.local` but not `.env`. If you clone and then point the app at your
   own database, put the value in `.env.local` so your connection string is not
   committed by accident.

   Without `MONGO_URL`, `mongoose.connect` throws; `connectToDB` logs the error and
   returns, and the route handlers do not check for failure, so requests error out
   rather than degrading.

4. Start the development server

   ```sh
   npm run dev
   ```

   Open [http://localhost:3000](http://localhost:3000).

   The port is not pinned in `next.config.mjs` — the config is empty — so 3000 is the
   Next.js default. It is also hardcoded in the client: `app/ContextApi.js` and the
   builder and player call `http://localhost:3000/api/...` directly rather than using
   relative paths, so a different port will break them.

5. Apply the linter (optional)

   ```sh
   npm run lint
   ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- USAGE EXAMPLES -->
## Usage

### Scripts

| Command | Action |
|---|---|
| `npm run dev` | Development server with hot reload |
| `npm run build` | Production build |
| `npm run start` | Serve the production build |
| `npm run lint` | ESLint via `next lint` |

### Routes

| Route | File | Purpose |
|---|---|---|
| `/` | `app/page.js` | Renders a work-in-progress layout sandbox, not the quiz list |
| `/quiz-build` | `app/quiz-build/page.jsx` | Create or edit a quiz |
| `/quiz-start` | `app/quiz-start/page.jsx` | Play the quiz selected in the context; redirects to `/` if none is selected |
| `/api/quizzes` | `app/api/quizzes/route.js` | `GET` all · `POST` create · `PUT` update by `?id=` · `DELETE` by `?id=` |
| `/api/user` | `app/api/user/route.js` | `POST` returns the first user, creating one if the collection is empty · `PUT` updates `isLogged` and `experience` by `?id=` |

### Example: create a quiz over the API

```sh
curl -X POST http://localhost:3000/api/quizzes \
  -H 'Content-type: application/json' \
  -d '{
    "icon": "faCode",
    "quizTitle": "Javascript Quiz",
    "quizQuestions": [
      {
        "id": "b1a0f0e2-7f1e-4c1a-9f2e-6b1f0d3a5c77",
        "mainQuestion": "What is the purpose of JavaScript?",
        "choices": [
          "A. To style HTML elements",
          "B. To add interactivity to web pages",
          "C. To define the structure of a web page",
          "D. To perform server-side operations"
        ],
        "correctAnswer": 1,
        "answeredResult": -1,
        "statistics": {
          "totalAttempts": 0,
          "correctAttempts": 0,
          "incorrectAttempts": 0
        }
      }
    ]
  }'
```

The response is `{ "id": "<mongo _id>", "message": "The quiz has been created successfully." }`.

### Example: read the quiz list

```sh
curl http://localhost:3000/api/quizzes
```

Returns `{ "quizzes": [ ... ] }` — every quiz document, including each question's
`correctAnswer`.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ROADMAP -->
## Roadmap

Implemented today:

- [x] Quiz builder with icon picker, title, dynamic question list and `A`–`D` choices
- [x] Client-side validation on add-question and on save, surfaced through toasts
- [x] Quiz player with a 30-second per-question timer and graded multiple choice
- [x] Score panel with correct/incorrect breakdown, result emoji and replay
- [x] Per-question attempt statistics written back to MongoDB
- [x] Experience counter persisted on the user document
- [x] Full CRUD route handler for quizzes, and read/update for the user
- [x] Responsive layout with Tailwind, Font Awesome icons, `next/image` assets

Known gaps, taken directly from the current state of the code:

- [ ] Rebuild `app/page.js` — it is a layout sandbox (theme toggle, placeholder boxes, unused `Sidebar` and `Dashboard` components) and no longer renders `QuizzesArea`, which is imported but unused
- [ ] Replace the hardcoded `http://localhost:3000/api/...` URLs with relative paths so the app works off the default port
- [ ] Add real authentication — `POST /api/user` returns the first document in the collection and creates one only when the collection is empty, and the "Get Started" button just flips `isLogged` locally
- [ ] Delete or wire up the stubs: `app/Components/ScoreComponent.js` returns an empty `<div>` (the working score panel is a local component inside `QuizStartQuestions.js`), and `app/QuizzesData.js` is imported but its seed data is never read
- [ ] Move connection settings out of source — `next.config.mjs` is empty and the deployment target is not configured
- [ ] Validate input server-side — `PUT /api/quizzes` reads `id` from the query string but never calls `connectToDB()`, and neither route is parameterised
- [ ] Add a `LICENSE` file

See the [open issues](https://github.com/Elouahabi-Naoufal/smarter2/issues) for a full list of proposed features (and known issues).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Top contributors:

<a href="https://github.com/Elouahabi-Naoufal/smarter2/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Elouahabi-Naoufal/smarter2" alt="contrib.rocks image" />
</a>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- LICENSE -->
## License

No `LICENSE` file is currently bundled with this repository, so no license has been
granted for it. Until one is added, treat the code as all rights reserved and ask before
reusing it. Opening an issue to propose a license is welcome.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->
## Contact

Naoufal Elouahabi - [LinkedIn](https://www.linkedin.com/in/elouahabi-naoufal/) - elouahabi-naoufal1@gmail.com

Project Link: [https://github.com/Elouahabi-Naoufal/smarter2](https://github.com/Elouahabi-Naoufal/smarter2)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

* [Next.js](https://nextjs.org) — App Router, Route Handlers and `next/font`, which supplies the Poppins variable font used across the app
* [Mongoose](https://mongoosejs.com) — schema definitions and connection handling
* [Font Awesome](https://fontawesome.com) — the icon set used in the quiz builder
* [react-hot-toast](https://github.com/timolins/react-hot-toast) — validation and scoring notifications
* [Best README Template](https://github.com/othneildrew/Best-README-Template)
* [Img Shields](https://shields.io)
* [contrib.rocks](https://contrib.rocks/)
* [Choose an Open Source License](https://choosealicense.com)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/Elouahabi-Naoufal/smarter2.svg?style=for-the-badge
[contributors-url]: https://github.com/Elouahabi-Naoufal/smarter2/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/Elouahabi-Naoufal/smarter2.svg?style=for-the-badge
[forks-url]: https://github.com/Elouahabi-Naoufal/smarter2/network/members
[stars-shield]: https://img.shields.io/github/stars/Elouahabi-Naoufal/smarter2.svg?style=for-the-badge
[stars-url]: https://github.com/Elouahabi-Naoufal/smarter2/stargazers
[issues-shield]: https://img.shields.io/github/issues/Elouahabi-Naoufal/smarter2.svg?style=for-the-badge
[issues-url]: https://github.com/Elouahabi-Naoufal/smarter2/issues
[license-shield]: https://img.shields.io/github/license/Elouahabi-Naoufal/smarter2.svg?style=for-the-badge
[license-url]: https://github.com/Elouahabi-Naoufal/smarter2
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/elouahabi-naoufal/
[Next.js]: https://img.shields.io/badge/next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white
[Next-url]: https://nextjs.org/
[React.js]: https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB
[React-url]: https://reactjs.org/
[MongoDB]: https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white
[Mongoose]: https://img.shields.io/badge/Mongoose-880000?style=for-the-badge&logo=mongoose&logoColor=white
[TailwindCSS]: https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white
[TailwindCSS-url]: https://tailwindcss.com/
[FontAwesome]: https://img.shields.io/badge/Font_Awesome-339AF0?style=for-the-badge&logo=font-awesome&logoColor=white
[FontAwesome-url]: https://fontawesome.com