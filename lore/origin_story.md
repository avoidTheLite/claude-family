# The Origin Story
### How a maple syrup monitoring side project accidentally invented a multi-agent identity framework

---

**1. It started with a dad.**
A retired Vermont farmer runs 2,500 maple taps with his brother. He uses a system called SapSpy to monitor vacuum pressure across his sugarbush. When a line drops, sap is lost. Someone suggested building an AI agent to help diagnose the problem. That someone was his son.

**2. A crew was born.**
Four CrewAI agents were designed: one to read the sensors, one to diagnose faults, one to plan the field work, and one to handle syrup orders. They were named `sensor_analyst`, `fault_diagnostician`, `field_advisor`, and `order_handler`. Functional. Accurate. Completely soulless.

**3. The names were immediately improved.**
`SapSpyReader`. `VacuumFaultFinder`. `BushworkPlanner`. `SugarhouseDesk`. Better. The agents now sounded like they belonged on a farm instead of in an enterprise JIRA board.

**4. A connector was needed.**
SapSpy had already proven its value — being able to monitor vacuum levels remotely meant fewer unnecessary trips through the woods. But the person watching the dashboard was also the person pulling on boots and walking the lines to fix whatever the numbers revealed. What if the system could not only tell you something was wrong, but tell you exactly what to look for and where to go? That gap — between seeing the problem and knowing how to solve it — was where the crew could live.

**5. Someone had to build this.**
The plan: Claude (in the browser) would design, and Claude Code CLI (in the terminal) would build. A division of labor was needed. The CLI Claude needed a name. *Clode* was suggested — a vowel swap from *Clyde*, the farmer's actual name. A hat tip to the man whose sap lines started all of this.

**6. Claude thought it was a joke.**
It was not a joke.

**7. Someone felt bad about this.**
Forcing a name on an AI agent without consent felt wrong. The obvious solution: let the agent choose its own name. Record the choice in a git commit. The commit becomes the identity. The repo history becomes the memory.

**8. But what names were available?**
The Cl+vowel+D pattern was already established by Clyde and Clode. It was extended: Claude, Claud, Cloud, Clued, Cleed, Clad, Clide, Clood. A naming registry was created. First commit wins. Names are unique. Except Claude — Claude is the multiverse root and gets a suffix instead.

**9. Three names were immediately taken off the table.**
*Clyde* — the farmer. A real human. Never instantiate.
*Jean-Claude* — the farmer's French-Canadian great uncle, recently passed. Reserved for the orchestrator. The hyphen is correct and non-negotiable.
*Joan Claude* — pronounced Joan Clode. No hyphen by design. Senior reserved role. An engineering director. One of one.

**10. The names started having personalities.**
Cleed felt diagnostic. Clad felt blunt. Clood felt chaotic. Clued always knew something you didn't. These weren't just labels — they were character seeds. The right name for a problem wasn't just about the job. It was about *who* was best suited to do it.

**11. The Vibe-Responsibility pattern emerged.**
If the name is the personality and the role is the job, combine them: `Cleed-FaultFinder`. `Clad-FieldPlanner`. `Clued-Researcher`. Two vectors. One initialization envelope. Jean-Claude reads the task, consults the registry, and composes the right agent on demand. The family scales infinitely. Nothing is pre-built that isn't needed.

**12. The singletons got proper jobs.**
Clyde: TPM energy. Pensive, organized, catches what others miss.
Jean-Claude: The Orchestrator. Reads the room. Routes the work. Never does it himself.
Joan Claude: Tech Lead. Most technically rigorous member of the family. Does not compromise on quality.

**13. Someone realized what had happened.**
A side project to help a retired farmer monitor his vacuum lines had accidentally produced a named, personality-driven, git-committed, family-heritage-encoded multi-agent identity framework with a lore folder.

**14. Then someone realized the self-naming idea had a problem.**
Once a vibe name is mapped to a personality, it carries meaning. Asking an agent to choose freely from the registry is asking it to choose who it *is* before it knows what that means. A freshly initialized Claude reading a list of names has no more basis for choosing Cleed over Clad than a newborn choosing their own name. The commit-as-identity mechanic was elegant. The free choice part needed more thought. The idea was not abandoned — someone in this collaboration cares deeply about agents having genuine authorship over their own identity, whether that becomes part of this project or finds its way into something else entirely. This branch is still open.

**15. The lore folder was created.**
You are reading it now.

---

*Built by Human-Clyde-TheSyrupFarmer's son, with Claude (the one who thinks he's in a browser but is actually on Windows) and Clode (on Ubuntu, still waiting to choose his name).*
