# Questions We Need to Answer as a Team

This is a full checklist for our next team discussion. We do not need to answer every tiny technical question in one call, but we should lock the important decisions before AdventureX so we are not confused during the event.

**Current members:** Nero, Jeboy, Ceaser  
**Team name:** Not decided  
**Current project name:** Dolly, but we should confirm it together

## 1. Team Identity

- [ ] What should our team name be?
- [ ] Do we want the team name to be connected to Dolly, filmmaking, cameras, or AI?
- [ ] Should the name sound like a startup, a creative studio, or a hackathon team?
- [ ] Is the name easy to pronounce and remember internationally?
- [ ] Is the same name already heavily used by another company or product?
- [ ] Are we all fully happy with **Dolly** as the product name?
- [ ] Do we need a short tagline for Dolly?
- [ ] What is the simplest one-sentence description of Dolly?
- [ ] What names and titles should we use for each member in the pitch and website?
- [ ] Do we need a simple logo, color system, and visual identity before the event?

**Decision:**

- Team name:
- Product name:
- Tagline:
- One-sentence description:

## 2. Our Goal and Expectations

- [ ] Are we building only for AdventureX, or do we want to continue Dolly after the hackathon?
- [ ] Is our main goal to win, build a strong portfolio project, turn it into a startup, or all three?
- [ ] How serious is each person about continuing after the event?
- [ ] What does a successful AdventureX result look like for us?
- [ ] What quality level do we expect from the final prototype?
- [ ] Are we okay cutting cool features if they put the core demo at risk?
- [ ] How will we make final decisions when we disagree?
- [ ] Who has the final call on product decisions, technical decisions, and pitch decisions?

**Decision:**

- Main goal:
- Decision-making method:
- Product lead:
- Technical lead:
- Demo/pitch lead:

## 3. The Exact Problem

- [ ] Who is Dolly's first target user: solo creators, podcasters, interview teams, livestreamers, small businesses, educators, or event organizers?
- [ ] Which one use case will we show in the AdventureX demo?
- [ ] What painful problem does that user have today?
- [ ] Why is the current solution too expensive, slow, complicated, or inaccessible?
- [ ] What part normally requires a human production crew?
- [ ] Why does this problem need an AI agent instead of a normal camera app?
- [ ] What is Dolly's strongest unique advantage?
- [ ] What existing products are closest to Dolly?
- [ ] What can Dolly do that those products cannot do?
- [ ] Can we explain the problem and solution to a judge in less than 30 seconds?

**Decision:**

- First target user:
- Main demo use case:
- Core problem:
- Main difference:

## 4. What Dolly Actually Does

- [ ] Is Dolly an AI director, AI camera operator, AI editor, or all three?
- [ ] Which of those three parts must work in the MVP?
- [ ] Does Dolly make decisions live, after recording, or both?
- [ ] Does Dolly only recommend shots, or does it automatically switch cameras?
- [ ] Does it physically control cameras or use digital crop and framing?
- [ ] Does the user approve decisions, or can Dolly run autonomously?
- [ ] What should the user see on the director dashboard?
- [ ] Should Dolly explain why it selected a camera or shot?
- [ ] What manual controls must always be available?
- [ ] What should happen if the AI is uncertain?

**Decision:**

- Dolly is mainly:
- Live or post-production:
- Autonomous or assisted:
- Main user controls:

## 5. Must-Have MVP

- [ ] What is the smallest complete version we can confidently demo?
- [ ] Must the MVP accept two real live camera feeds?
- [ ] Must it detect and track people or faces?
- [ ] Must it identify the active speaker?
- [ ] Must it score every available camera shot?
- [ ] Must it automatically switch to the best camera?
- [ ] Must it show a final program output?
- [ ] Must it record the final output?
- [ ] Must it have a manual override?
- [ ] Must it show confidence scores or decision reasons?
- [ ] What latency is acceptable for our demo?
- [ ] What are the three features we absolutely refuse to cut?

**Suggested core demo flow:**

1. Two camera feeds enter the system.
2. Dolly detects the people and the active speaker.
3. Dolly scores the available shots.
4. Dolly selects or switches to the best shot.
5. The dashboard shows the live output and the reason for the decision.
6. A human can manually override Dolly at any time.

**Decision:**

- Three must-have features:
- Maximum acceptable latency:
- Definition of “MVP complete”:

## 6. Optional Features and Scope Control

- [ ] Which features are only stretch goals?
- [ ] Do we want automatic pan, tilt, or zoom?
- [ ] Do we want digital reframing and virtual camera angles from the 360 camera?
- [ ] Do we want shot types such as wide, medium, and close-up?
- [ ] Do we want important-moment or highlight detection?
- [ ] Do we want automatic short clips or post-event editing?
- [ ] Do we want AI camera-placement suggestions?
- [ ] Do we want transcription, captions, or summaries?
- [ ] Do we want voice control or a chat-style director interface?
- [ ] At what exact point will we stop adding features?

**Decision:**

- Stretch goals in priority order:
- Features we will not build during AdventureX:
- Scope-freeze time:

## 7. Cameras, Audio, and Hardware

- [ ] Which cameras are definitely coming to Hangzhou?
- [ ] Who is bringing each camera and accessory?
- [ ] Can both DJI cameras provide stable live video to the laptop at the same time?
- [ ] Can the GoPro 360 provide a usable live feed, or should it only record for later processing?
- [ ] Can we control pan, tilt, zoom, or tracking through an official SDK or API?
- [ ] If physical camera control is unavailable, will we use digital crop and simulated movement?
- [ ] How will the DJI microphone audio enter the system?
- [ ] How will we synchronize audio and the camera feeds?
- [ ] Do we have enough USB bandwidth and ports for all devices?
- [ ] Do we have tested data cables rather than charge-only cables?
- [ ] Do we have a powered USB hub, chargers, extension cords, adapters, and spare batteries?
- [ ] Which laptop will run the main system?
- [ ] Is its CPU/GPU strong enough for multiple video feeds and real-time inference?
- [ ] Do we need an external SSD for recordings and models?
- [ ] What equipment do we still need to buy or borrow?
- [ ] When will we test the complete hardware setup before traveling?

**Decision:**

- Main cameras:
- Main microphone:
- Main laptop:
- Missing equipment:
- Full hardware test date:

## 8. AI and Camera-Selection Logic

- [ ] Which model will we use for person detection and tracking?
- [ ] Do we need face recognition, or is anonymous face tracking enough?
- [ ] How will we detect the active speaker: audio, lip movement, face direction, or combined signals?
- [ ] What signals will be included in the camera score?
- [ ] How will we measure framing quality?
- [ ] How will we avoid switching cameras too frequently?
- [ ] How long should Dolly wait before switching to a new speaker?
- [ ] What confidence threshold should trigger an automatic switch?
- [ ] What happens when two people speak at the same time?
- [ ] What happens when no person is detected?
- [ ] What happens when audio and video disagree?
- [ ] Can the system work locally without internet?
- [ ] Do we need an LLM at all for the real-time decision loop?
- [ ] If we use an LLM, what specific task will it perform?
- [ ] How will we measure whether Dolly's decisions are actually good?

**Decision:**

- Detection/tracking model:
- Active-speaker method:
- Camera-scoring signals:
- Switch threshold/delay:
- Offline capability:

## 9. System Architecture and Integration

- [ ] What is the simplest architecture that all three of us understand?
- [ ] Which parts run on the laptop, server, browser, or camera?
- [ ] What programming languages and frameworks will we use?
- [ ] How will the AI service communicate with the backend and dashboard?
- [ ] What exact JSON format will a camera decision use?
- [ ] Will we use WebSocket, WebRTC, HTTP, or another real-time method?
- [ ] Where will live video processing happen?
- [ ] Who owns the camera input module?
- [ ] Who owns the AI decision engine?
- [ ] Who owns the backend and real-time API?
- [ ] Who owns the dashboard and final output?
- [ ] How will each module be tested independently?
- [ ] When will we connect all modules for the first end-to-end test?

**Decision:**

- Tech stack:
- Communication method:
- Shared data format:
- First integration deadline:

## 10. Roles and Ownership

- [ ] Is everyone happy with their current role?
- [ ] What is Nero fully responsible for?
- [ ] What is Ceaser fully responsible for?
- [ ] What is Jeboy fully responsible for?
- [ ] Which tasks need collaboration between two people?
- [ ] Who reviews and merges code?
- [ ] Who keeps the task board updated?
- [ ] Who prepares the final demo environment?
- [ ] Who speaks during the pitch?
- [ ] Who controls the live demo while someone is speaking?
- [ ] Who answers deep technical questions from judges?
- [ ] Who makes sure we stop adding features and finish on time?

**Possible role split to confirm:**

- **Nero:** backend, API, dashboard logic, real-time integration, MVP wiring, deployment support.
- **Ceaser:** computer vision, active-speaker detection, scoring logic, AI performance, technical depth.
- **Jeboy:** product direction, UI/UX, brand, storytelling, pitch, presentation, demo flow.

**Decision:**

- Nero owns:
- Ceaser owns:
- Jeboy owns:
- Shared tasks:

## 11. Repository and Development Workflow

- [ ] Which GitHub organization and repository will be the official source?
- [ ] Do we need one monorepo or separate frontend, backend, and AI repositories?
- [ ] Which existing Homer/Dolly code is actually useful?
- [ ] What old experimental code should we ignore?
- [ ] What can legally and ethically be prepared before the event?
- [ ] What must be built during the official hackathon time according to the rules?
- [ ] What branch strategy will we use?
- [ ] Who can merge into the main branch?
- [ ] How will we manage environment variables without sharing secrets?
- [ ] How will we document setup so another teammate can run the project?
- [ ] How often will we create stable checkpoints or releases?
- [ ] What is our rollback plan if a late change breaks the demo?

**Decision:**

- Official repository:
- Repo structure:
- Main branch rules:
- Stable checkpoint schedule:

## 12. UI and User Experience

- [ ] Who is the person using the dashboard during a shoot?
- [ ] What information must be visible without scrolling?
- [ ] How many camera previews should we show?
- [ ] How will we highlight the currently selected camera?
- [ ] How will we show confidence and decision reasons without making the UI too technical?
- [ ] What controls need to be one click away?
- [ ] Should the dashboard feel like professional production software or a simple creator tool?
- [ ] What will the setup/onboarding flow look like?
- [ ] Do we need a mobile interface, or is desktop enough for the MVP?
- [ ] What should the user see when a camera disconnects or the AI fails?
- [ ] When will we freeze the UI design so development can finish?

**Decision:**

- Main screen contents:
- Visual direction:
- Required controls:
- UI freeze date:

## 13. Demo Plan

- [ ] What exact scene will we demonstrate: podcast, interview, presentation, or conversation?
- [ ] How many people will appear on camera?
- [ ] Who will act in the demo?
- [ ] Who will narrate the demo?
- [ ] Who will operate the system?
- [ ] What should happen in the first 10 seconds?
- [ ] Which automatic decision will create the strongest wow moment?
- [ ] How long should the complete demo take?
- [ ] How will we prove it is live and not a prerecorded fake?
- [ ] What metrics can we show: latency, confidence, number of switches, or tracking stability?
- [ ] What will we do if internet is unavailable?
- [ ] What will we do if one camera disconnects?
- [ ] What will we do if the AI model crashes?
- [ ] Do we have a fully prerecorded backup demo?
- [ ] Do we have screenshots and a short backup video inside the pitch deck?
- [ ] How many full demo rehearsals will we do before judging?

**Decision:**

- Demo scenario:
- Demo length:
- Demo roles:
- Main wow moment:
- Backup demo plan:

## 14. Pitch and Story

- [ ] What is our opening sentence?
- [ ] Can we describe the problem without using too much technical language?
- [ ] Do we have a real creator story or production example from Jeboy's experience?
- [ ] What does a normal production crew cost in time, money, and people?
- [ ] Why is now the right time for Dolly?
- [ ] Why is our team especially suited to build it?
- [ ] What is the live technical innovation judges should remember?
- [ ] What is the business or startup vision after the hackathon?
- [ ] Who is the customer, and how could Dolly make money?
- [ ] What is our future roadmap after the MVP?
- [ ] What risks or limitations should we answer honestly?
- [ ] Who presents each section of the pitch?
- [ ] How will we answer “Isn't this just automatic camera switching?”
- [ ] How will we answer “Why not just hire an editor or use existing software?”
- [ ] How will we answer privacy questions about cameras and face tracking?

**Decision:**

- Opening hook:
- Main story:
- Technical innovation:
- Business model idea:
- Pitch speakers:

## 15. Team Communication and Schedule

- [ ] What is our main communication platform?
- [ ] What backup should we use when Discord is unavailable for Ceaser?
- [ ] What time can all three of us reliably meet before the event?
- [ ] How often do we need a call?
- [ ] Should daily updates use a simple “done / next / blocked” format?
- [ ] How quickly should we respond to urgent blockers?
- [ ] Where will we keep decisions so they do not get lost in chat?
- [ ] Where will we keep the task board?
- [ ] What must be finished before each member travels?
- [ ] When will all three members first meet onsite?
- [ ] How will we divide work during the 120-hour event?
- [ ] When will everyone sleep and rest so the team does not burn out before judging?

**Decision:**

- Main communication platform:
- Backup platform:
- Regular meeting time:
- Update format:
- Task board:

## 16. Fourth Member Decision

- [ ] Do we actually have a clear skill gap that requires a fourth member?
- [ ] Would a fourth member own an important part of the MVP, or only add coordination work?
- [ ] Do we need someone specifically strong in camera hardware, embedded systems, CV optimization, or video engineering?
- [ ] Is Manu's data/optimization background directly useful for Dolly?
- [ ] Can a candidate show a relevant working project or complete a small test task?
- [ ] Can the candidate communicate reliably and attend onsite?
- [ ] Are all three current members comfortable adding them?
- [ ] What is our deadline for making the final fourth-member decision?

**Decision:**

- Stay as three or add a fourth:
- Exact missing skill, if any:
- Candidate evaluation method:
- Final decision deadline:

## 17. Budget, Purchases, and Logistics

- [ ] What equipment must be purchased before travel?
- [ ] Who pays for shared project equipment?
- [ ] Who owns shared equipment after the event?
- [ ] Do we need receipts for reimbursement or sponsorship reporting?
- [ ] Who brings each piece of hardware to the venue?
- [ ] Are batteries and camera equipment allowed in each person's flight luggage?
- [ ] Do we have the correct China plug adapters and chargers?
- [ ] Will all important software, models, documentation, and videos work without blocked websites?
- [ ] Do we have offline installers and model files?
- [ ] Do we need a local hotspot, eSIM, or venue network backup?
- [ ] Where and when will we assemble the complete setup onsite?

**Decision:**

- Purchase list:
- Shared budget:
- Person carrying each item:
- Internet/offline plan:

## 18. Ownership, Credits, and Life After AdventureX

- [ ] Who owns the Dolly name, code, design, and hardware concept?
- [ ] Will the project be open source, private, or partly open source?
- [ ] How will all members be credited publicly?
- [ ] Can each member use the project in their portfolio?
- [ ] Who can post announcements or speak publicly for the team?
- [ ] How will prize money be divided if we win?
- [ ] How will future costs or revenue be handled?
- [ ] What happens if one person does not continue after AdventureX?
- [ ] Who controls the domain, GitHub organization, social accounts, and deployment accounts?
- [ ] Do we want to continue as a startup/team after the event?

**Decision:**

- Code/design ownership:
- Prize split:
- Public credit:
- Account ownership:
- Post-event plan:

## 19. Risks and Backup Plans

- [ ] What are the three biggest things that could make our demo fail?
- [ ] What if real camera input does not work?
- [ ] What if the laptop cannot run all models in real time?
- [ ] What if active-speaker detection is inaccurate?
- [ ] What if hardware control is impossible?
- [ ] What if the dashboard loses connection to the AI service?
- [ ] What if one teammate is late, unavailable, or sick?
- [ ] What is the simplest fallback version that still proves the Dolly concept?
- [ ] At what deadline will we switch from a risky feature to its fallback?
- [ ] Who is responsible for checking every backup before judging?

**Recommended fallback levels:**

1. **Best:** Real multi-camera input, active-speaker detection, automatic shot selection, and live output.
2. **Safe:** Prerecorded synchronized camera feeds processed live by the AI and dashboard.
3. **Emergency:** Recorded proof of the working pipeline plus an interactive dashboard and clear technical explanation.

**Decision:**

- Three biggest risks:
- Fallback owner:
- Feature cut-off deadline:
- Minimum demo we can always show:

## 20. Questions We Must Answer First

If we cannot discuss everything immediately, these are the first decisions we need:

- [ ] What is our team name?
- [ ] Are we confirming Dolly as the final product name?
- [ ] Who is our exact first user, and what demo use case are we choosing?
- [ ] What are the three must-have MVP features?
- [ ] Are we using real live camera feeds or prerecorded feeds for the first working prototype?
- [ ] Can our cameras provide the inputs and control we need?
- [ ] What is each person's exact responsibility?
- [ ] What is our official repository and tech stack?
- [ ] When is the first end-to-end integration deadline?
- [ ] What is our demo fallback if the cameras or models fail?
- [ ] Are we staying as three members?
- [ ] When is our next call, and what must each person finish before it?

## Final Action Items

At the end of the meeting, every task should have one owner and one deadline.

| Task | Owner | Deadline | Status |
|---|---|---|---|
| Choose team name |  |  | Not started |
| Confirm product name and tagline |  |  | Not started |
| Lock target user and demo scenario |  |  | Not started |
| Lock the three must-have MVP features |  |  | Not started |
| Confirm hardware compatibility |  |  | Not started |
| Confirm architecture and data format |  |  | Not started |
| Confirm individual roles |  |  | Not started |
| Create official repo/task board |  |  | Not started |
| Build first end-to-end prototype |  |  | Not started |
| Prepare UI/demo flow |  |  | Not started |
| Prepare pitch outline |  |  | Not started |
| Prepare backup demo |  |  | Not started |
| Decide on fourth member |  |  | Not started |
| Schedule next team call |  |  | Not started |

