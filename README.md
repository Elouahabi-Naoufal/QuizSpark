<a id="readme-top"></a>

[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

<br />
<div align="center">
  <h3 align="center">QuizSpark</h3>

  <p align="center">
    A Next.js 14 quiz builder and player: create multiple-choice quizzes, play them under a
    per-question timer, and persist questions, attempts and experience in MongoDB.
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/QuizSpark"><strong>Explore the source »</strong></a>
    <br />
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/QuizSpark/issues/new?labels=bug">Report Bug</a>
    &middot;
    <a href="https://github.com/Elouahabi-Naoufal/QuizSpark/issues/new?labels=enhancement">Request Feature</a>
  </p>
</div>

<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
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

## About The Project

QuizSpark is a Next.js 14 App Router project written in JavaScript. It stores quizzes and a
single player profile in MongoDB through Mongoose and talks to the database only through Next.js
Route Handlers.

The current snapshot implements:

* A quiz builder at `/quiz-build` with a title, Font Awesome icon, and questions with four
  choices and a correct-answer index
* A player at `/quiz-start` with a 30-second timer per question, answer feedback, scoring and a
  result panel
* Per-question statistics: total attempts, correct attempts and incorrect attempts
* A user experience counter persisted between sessions
* REST-style route handlers for quizzes (`GET`, `POST`, `PUT`, `DELETE`) and the user profile
  (`POST`, `PUT`)
* A global React context that loads the quiz list and player state on mount

The synchronous group mode, evolving difficulty and multilingual interface described in the wider
SMARTER project scope are not present in this snapshot yet; they are listed in the roadmap below.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Built With

* [![Next.js][Next.js]][Next-url]
* [![React][React.js]][React-url]
* [![MongoDB][MongoDB]][MongoDB-url]
* [![Mongoose][Mongoose]][Mongoose-url]
* [![Tailwind CSS][TailwindCSS]][TailwindCSS-url]
* [![Font Awesome][FontAwesome]][FontAwesome-url]

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Getting Started

### Prerequisites

* Node.js 18.17 or newer
* npm (the repository includes a `package-lock.json`)
* A MongoDB database — local or Atlas

### Installation

1. Clone the repository
   ```sh
   git clone https://github.com/Elouahabi-Naoufal/QuizSpark.git
   cd QuizSpark
   ```

2. Install dependencies
   ```sh
   npm install
   ```

3. Provide the MongoDB connection string. The connection helper reads `MONGO_URL`:
   ```sh
   export MONGO_URL="mongodb://127.0.0.1:27017/quizspark"
   ```

4. Start the development server
   ```sh
   npm run dev
   ```

5. Open <http://localhost:3000>

For a production build:
```sh
npm run build
npm start
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Usage

1. Open `/quiz-build` to create a quiz. Enter a title, choose an icon, then add questions.
2. Each question has four choices (`A.`–`D.`) and a correct-answer index. Incomplete questions are
   rejected with a toast.
3. Save the quiz. The player lives at `/quiz-start`; this snapshot does not yet include a wired
   lobby or navigation from the home page.
4. During play, each question has a 30-second limit. A correct answer adds to the score and
   experience; an incorrect answer or timeout is recorded as incorrect.
5. At the end, the result panel shows the score, the correct/incorrect split and a Try Again
   option. Question statistics are written back to MongoDB.

The API is available under `/api/quizzes` and `/api/user`.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Roadmap

- [x] Quiz builder with icons, questions, choices and correct answers
- [x] Timed single-player quiz runner with score and experience
- [x] MongoDB persistence through Mongoose route handlers
- [x] Per-question attempt statistics
- [ ] Add the synchronous group/room mode and live leaderboard
- [ ] Add multilingual UI and content
- [ ] Add real user accounts and isolate each player's quizzes and statistics
- [ ] Replace the hard-coded single-user bootstrap in `/api/user`
- [ ] Add automated tests for grading and timer behaviour
- [ ] Replace the placeholder home page with the quiz lobby

See the [open issues](https://github.com/Elouahabi-Naoufal/QuizSpark/issues) for a full list of
proposed features and known issues.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire,
and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Top contributors:

<a href="https://github.com/Elouahabi-Naoufal/QuizSpark/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Elouahabi-Naoufal/QuizSpark" alt="contrib.rocks image" />
</a>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## License

This project is not licensed — all rights reserved.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Contact

Naoufal Elouahabi - [LinkedIn](https://www.linkedin.com/in/elouahabi-naoufal/) - elouahabi-naoufal1@gmail.com

Project Link: [https://github.com/Elouahabi-Naoufal/QuizSpark](https://github.com/Elouahabi-Naoufal/QuizSpark)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Acknowledgments

* [Next.js documentation](https://nextjs.org/docs)
* [Mongoose documentation](https://mongoosejs.com/docs/)
* [Font Awesome](https://fontawesome.com/)
* [Best-README-Template](https://github.com/othneildrew/Best-README-Template)
* [Img Shields](https://shields.io)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

[contributors-shield]: https://img.shields.io/github/contributors/Elouahabi-Naoufal/QuizSpark.svg?style=for-the-badge
[contributors-url]: https://github.com/Elouahabi-Naoufal/QuizSpark/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/Elouahabi-Naoufal/QuizSpark.svg?style=for-the-badge
[forks-url]: https://github.com/Elouahabi-Naoufal/QuizSpark/network/members
[stars-shield]: https://img.shields.io/github/stars/Elouahabi-Naoufal/QuizSpark.svg?style=for-the-badge
[stars-url]: https://github.com/Elouahabi-Naoufal/QuizSpark/stargazers
[issues-shield]: https://img.shields.io/github/issues/Elouahabi-Naoufal/QuizSpark.svg?style=for-the-badge
[issues-url]: https://github.com/Elouahabi-Naoufal/QuizSpark/issues
[license-shield]: https://img.shields.io/badge/license-none-lightgrey?style=for-the-badge
[license-url]: #license
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/elouahabi-naoufal/
[Next.js]: https://img.shields.io/badge/next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white
[Next-url]: https://nextjs.org/
[React.js]: https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB
[React-url]: https://react.dev/
[MongoDB]: https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white
[MongoDB-url]: https://www.mongodb.com/
[Mongoose]: https://img.shields.io/badge/Mongoose-880000?style=for-the-badge&logo=mongoose&logoColor=white
[Mongoose-url]: https://mongoosejs.com/
[TailwindCSS]: https://img.shields.io/badge/Tailwind%20CSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white
[TailwindCSS-url]: https://tailwindcss.com/
[FontAwesome]: https://img.shields.io/badge/Font%20Awesome-528DD7?style=for-the-badge&logo=fontawesome&logoColor=white
[FontAwesome-url]: https://fontawesome.com/
