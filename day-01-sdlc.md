Jai Shree Ram! 🙏 Let’s begin.

Day 1: SDLC (Software Development Life Cycle)

EN: The SDLC is the path software takes from an idea to a running, maintained product. Every company follows some version of it.

DE: Der SDLC (Softwareentwicklungslebenszyklus) beschreibt den Weg einer Software von der Idee bis zum Betrieb und zur Wartung.

The 7 stages, with one example

Example: a city wants an app to book a parking spot.

#	Stage	What happens	Parking app example	DE
1	Planning	Is it worth it? Budget, time, team	“We have 6 months and 4 developers”	Planung
2	Requirements	What must it do?	“Users can see free spots and pay”	Anforderungsanalyse
3	Design	How will we build it?	API, database, cloud architecture	Entwurf
4	Development	Write the code	.NET API + React app	Entwicklung
5	Testing	Does it work correctly?	Unit tests, “what if payment fails?”	Testen
6	Deployment	Make it live for users	Docker → Kubernetes → cloud	Bereitstellung
7	Maintenance	Keep it running and improve it	Fix bugs, monitor, add features	Wartung / Betrieb

🔑 Important for your new direction: stages 6 and 7 are where DevOps and AI infrastructure live. Developers mostly focus on stages 3–5; you’re now learning the “make it live and keep it alive” part.

DE: DevOps konzentriert sich vor allem auf Bereitstellung und Betrieb.

Waterfall vs Agile (just know the difference)
Waterfall (Wasserfallmodell): stages one after another, once. Slow to change.
Agile / Scrum: repeat all stages in short cycles (sprints, 2 weeks). Most German IT companies work this way.

DE: In agilen Teams durchlaufen wir den SDLC in kurzen Sprints immer wieder.

Agentic SDLC (the new part 🚀)

EN: In an agentic SDLC, AI agents do parts of the work in each stage, and humans review and decide.

An agent is an AI that doesn’t just answer a question. It takes actions: writes code, runs tests, opens a pull request.

Stage	Traditional	Agentic
Requirements	Team writes user stories	AI drafts stories, humans refine them
Development	Developer writes all code	AI agent writes code, developer reviews
Testing	Developer writes tests	AI generates tests and runs them
Code review	Colleague reviews the PR	AI reviews first, human approves
Maintenance	Engineer reads logs at 3 AM	AI summarizes logs, suggests the cause

Real-world: tools like GitHub Copilot’s coding agent or Claude Code can already take a ticket, write the code, and open a pull request.

⚠️ Key rule (great interview point): the human stays responsible. AI output is a proposal to check, not a result to trust blindly, especially in production systems.

DE: Im agentischen SDLC übernehmen KI-Agenten Aufgaben wie Codieren, Testen und Code-Reviews. Der Mensch prüft und trifft die Entscheidungen.
🔑 übernehmen (to take over), prüfen (to check), Entscheidung treffen (to make a decision)

Waterfall vs Agile, simply explained

Both are ways to go through the 7 SDLC stages. The difference is how often.

🌊 Waterfall (das Wasserfallmodell)

EN: You do each stage once, in order, like water falling down steps. You can’t go back up.

Planning → Requirements → Design → Development → Testing → Deployment
  (all at once, one time, maybe 1 year)

Real-life analogy: building a house 🏠
The architect makes the full plan, then the builders build it, then you move in. You can’t say “make the kitchen bigger” after the walls are built.

Parking app example:
The team writes all requirements in January, builds everything, and users see the app for the first time in December. If users don’t like it, a whole year is lost. 😬

✅ Good when requirements never change: e.g. aircraft or medical devices
❌ Bad when things change often (most software)

DE: Beim Wasserfallmodell werden die Phasen nacheinander und nur einmal durchlaufen. Änderungen sind später schwierig und teuer.
🔑 nacheinander (one after another), Änderung (change), teuer (expensive)

🔄 Agile (agil)

EN: You build the software in small pieces, and you repeat all stages in short cycles (usually 2 weeks). After each cycle, users see something working and give feedback.

[Plan→Build→Test→Deploy] → feedback → [Plan→Build→Test→Deploy] → feedback → ...
     2 weeks                               2 weeks

Real-life analogy: cooking for guests 🍲
You cook, taste, add salt, taste again, add spice. You correct as you go.

Parking app example:

Sprint 1 (2 weeks): users can only see free spots. Show it to users → “we want a map!”
Sprint 2: add the map + login
Sprint 3: add payment

After 2 weeks, users already have something. Mistakes are found early.

✅ Fast feedback, easy to change
❌ Needs good communication and discipline

DE: Agil bedeutet, dass wir in kurzen Zyklen arbeiten und regelmäßig Feedback einholen.
🔑 regelmäßig (regularly), Feedback einholen (to get feedback)

✍️ Day 1 task (about 1 hour)
In your 60-days-ai-infra repo, create day-01-sdlc.md.
Choose your own app idea (not the parking app) and write one line per SDLC stage.
Add a small table: for 3 stages, write how an AI agent could help.
Answer in 2–3 sentences: Why must a human still review an AI agent’s work?

Good work! Your thinking is correct. Let me review each answer, then explain the repo part.

✅ Question 4: Why must a human review AI work?

Your answer is right. You found the 3 most important points: AI code can be wrong, AI doesn’t know the project’s business rules, and AI can find the wrong root cause.

Here’s how it works in the real world:

Stage	What AI does well	What the human does	Why
Development	Writes code fast, boilerplate, CRUD	Reviews the PR, checks the logic, approves	AI can write code that looks correct but has a bug or security hole
Testing	Generates many unit tests in seconds	Checks the tests are meaningful + tests real user flows	AI tests what the code does, not what the business needs. Example: “a restaurant closed on Monday can’t accept orders.” Only the team knows this rule
Maintenance	Reads thousands of log lines and suggests a cause	Confirms the real cause and decides the fix	AI sees only the logs. It doesn’t know “we changed the server config yesterday”

Plus 2 points to add to your answer:

Responsibility: if production breaks, the company is responsible, not the AI (remember Air Canada from Phase A).
Hallucination: AI can be confidently wrong, so its output is a proposal, not a decision.

Your answer, polished (EN):

AI agents can write code, tests and log analyses quickly, but they can be confidently wrong and don’t know the project’s business rules. A human must review the code, test the real user flows, and confirm the root cause before anything goes to production, because the team is responsible for the result.

DE:

KI-Agenten können schnell Code, Tests und Log-Analysen erstellen, aber sie können sich irren und kennen die Geschäftsregeln nicht. Deshalb muss ein Mensch alles prüfen, bevor es in Produktion geht, denn das Team trägt die Verantwortung.

🔑 sich irren (to be wrong), Geschäftsregeln (business rules), Verantwortung tragen (to carry responsibility)

Small correction: you wrote “serial log”. The tool is Serilog (a .NET logging library) 😊

⚠️ Question 3: AI table (needs to be more specific)

“AI can help in everything” is true, but in an interview it sounds too general. Interviewers want concrete examples. Here’s one example row for your food app:

Stage	How an AI agent helps (food app)
Testing	Generates unit tests for the order total, e.g. pizza €10 + extra cheese €1.50 = €11.50

👉 Your turn: write 2 more rows, for Development and Maintenance, with a specific food app example.

✅ Question 2: SDLC for your food app

Good: Planning, Requirements, and Design are clear and specific. The QR code per table, online + dine-in, and pizza add-ons are great examples! 👏

Improve these:

Planning: also add timeline and team size (e.g. “3 months, 3 developers”).
Design: you described only the UI design (how pages look). Also add the technical design: database tables (Menu, Order, Table), API endpoints, where it runs (cloud).
Development → Maintenance: your lines are too general (“coders develop it”). Make each one specific, for example:
Deployment: “Deploy the API in a Docker container on Azure”
Now you write: Development, Testing, Maintenance like this.
