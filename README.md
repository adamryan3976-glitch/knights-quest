# Knights' Quest: Winchester P.S. math adventure

One file (`index.html`) holds the whole game and the teacher dashboard. Out of the box it runs in **preview mode**: everything is saved in the browser of one device, and the teacher button opens a demo account. Connecting Firebase turns on DDSB Google sign-in for teachers and lets classes work across every Chromebook.

## Going live (about 20 minutes)

1. **Create a Firebase project** at console.firebase.google.com (or add to an existing one). Google Analytics is not needed.
2. **Add a web app** (the `</>` icon on the project overview). Copy the `apiKey`, `authDomain`, `projectId` and `appId` values it shows you.
3. **Authentication → Sign-in method**: enable **Google** and **Anonymous**. Teachers use Google; students are signed in anonymously behind the scenes so they never see a login screen.
4. **Authentication → Settings → Authorized domains**: add the domain you will host on, for example `yourname.github.io`.
5. **Firestore Database → Create database**. Choose production mode and a Canadian location (`northamerica-northeast2` is Toronto).
6. **Firestore → Rules**: paste the contents of `firestore.rules` and publish.
7. Open `index.html`, find the `CONFIG` block near the bottom (search for `SETUP`), and paste the four values from step 2.
8. **Host it.** Put `index.html` in a GitHub repository and turn on GitHub Pages, the same way as any static site.

Teachers then open the site, choose "I'm a Teacher", sign in with their @ddsb.ca account, and create a class. Each class gets a six-character code and a join link.

To allow another staff domain, add it to `teacherDomains` in `CONFIG` and to the `matches(...)` line in `firestore.rules`.

## Things to check before using it with students

- **Board approval.** The game stores a first name, a secret crest and math progress for each student in your Firebase project. Check DDSB's process for approving digital tools that hold student information before a full roll-out.
- **Google sign-in may be blocked at first.** Workspace administrators can restrict sign-in to apps they have not approved. If teachers see "access blocked", IT needs to trust the app's OAuth client ID (Firebase console → Authentication → Sign-in method → Google → Web SDK configuration).
- **Live mode is only partly proven.** Google sign-in has worked on the real project; class creation, joining and saving still need a first run-through there.
- **Security trade-off.** Anyone who knows a class code can see the first names in that class and could overwrite a student's progress. Class codes are random and the data is low-stakes, but treat the code like a classroom password.

## How it plays

Each quest is a side-scrolling run. The knight runs through the realm; the player jumps (Space, or tap) to grab coins and bop gloomlings. Every few seconds a Gloom Gate blocks the path, and the knight stops until the player solves its challenge, so there is never a clock on the math. First-try answers charge the shield; three charges set off a Shield Dash in the next stretch (fast, invincible, coins fly to you). Clearing every gate reaches the creature at the end of the run, and the shield beam frees it.

## Optional art pack (Stable Diffusion or any other source)

The scenery is drawn in code. To swap in painted backgrounds, create a folder named `art` beside `index.html` and add any of these files; the game picks them up automatically and falls back to the built-in scenery for any that are missing.

| File | Realm | Prompt idea |
|---|---|---|
| `art/bg-B.jpg` | Number Forest | storybook enchanted forest, side-scrolling game background, soft painted style, bright daylight, no characters, no text |
| `art/bg-C.jpg` | Pattern Peaks | storybook purple mountain range with snowy peaks, side-scrolling game background, painted style, no characters, no text |
| `art/bg-E.jpg` | Shape Citadel | storybook stone castle walls and towers with blue banners, side-scrolling game background, painted style, no characters, no text |
| `art/bg-D.jpg` | Data Docks | storybook harbour with sailing ships and calm sea, side-scrolling game background, painted style, no characters, no text |
| `art/bg-F.jpg` | Golden Market | storybook medieval market with striped tents at golden hour, side-scrolling game background, painted style, no characters, no text |

Use wide images (around 2048 x 512). They tile sideways, so images that are seamless left to right look best; keep the bottom edge plain because the ground is drawn over it.

## How it works

### Hidden pre-assessment
A new student plays "Knight Training": five short trials, one per strand, 22 questions in total. Each trial is a staircase. It starts one grade below the class grade, moves up after a correct answer and down after a miss, and settles on the grade where the student gets about half right. That becomes the student's working level for the strand. To the student it is the opening chapter of the story.

### Learning
After training, each battle draws mostly from unmastered skills at the working level, with about one question in five reviewing older skills (the one seen longest ago comes first). A skill counts as mastered after at least five tries with a strong recent record. When every skill at a level is mastered the strand moves up a grade; if a student gets fewer than 40% right over several questions it moves down one. Two misses in a row on a skill brings in a question from the grade below.

### Design choices and the research behind them
- **Retrieval and spaced practice**: review questions are mixed into every battle rather than taught once and dropped.
- **Feedback that teaches**: a first miss gives a strategy hint and a second try; a second miss shows a worked solution, then a similar question follows.
- **Models before symbols**: ten frames, base ten blocks, arrays, fraction bars, clocks and rulers appear with the early questions.
- **No timers, no leaderboards, no lost lives**: timed drills and public ranking raise math anxiety for many children. Progress shows as creatures befriended and skills mastered.
- **Low reading load**: every question has a read-aloud button, and it reads automatically in Kindergarten and Grade 1 classes.
- **Honest signal**: "I haven't learned this yet" lets a student skip without guessing, which keeps the pre-assessment cleaner.

### Curriculum coverage
136 question types, each tagged to one specific expectation: The Ontario Curriculum, Grades 1 to 8: Mathematics (2020) for Grades 1 to 8, and the Kindergarten Curriculum (2026) for Kindergarten. Every strand B to F has questions at every grade. Strand A (social-emotional learning and the mathematical processes) and coding (C3) are not assessed by the game. The dashboard's "Curriculum expectations" tab lists the codes; spot-check them against the curriculum site before relying on them for reporting, especially in Spatial Sense and Financial Literacy.

## Changing the game
The question bank is the `Skills` section of the script. Each entry has an id, strand letter, grade, expectation code, name, hint, and a small function that builds a random question. Adding an entry is enough for it to appear in the game and the dashboard.
