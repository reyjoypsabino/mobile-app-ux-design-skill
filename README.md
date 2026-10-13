# Mobile App UX Design Skill

A [Claude](https://claude.ai) skill that turns Claude into a cross-platform mobile UI/UX designer for **React Native** apps: one design that follows **Apple's Human Interface Guidelines** on iOS, **Material Design 3** on Android, and **WCAG 2.2 Level AA** accessibility on both.

**Author:** Reyjoy Sabino
**Built with:** Claude (Anthropic)
**Last verified:** October 2026 (iOS 26 / Liquid Glass, Android 16 / Material 3 Expressive)

---

## What it does

Gives Claude a complete, opinionated workflow for mobile design, from discovery to developer handoff:

| Phase | What happens |
|---|---|
| 1. Discover | Frame the problem, users, constraints; research and information architecture for new products |
| 2. Structure | User flows, navigation model, system decisions that shape UX (offline, state, errors, security) |
| 3. Design | Screens with every state, design tokens, components, UX writing, motion, accessibility |
| 4. Deliver and hand off | Clickable HTML mockups, Figma builds, React Native handoff package |
| 5. Review | HIG + Material 3 + WCAG checklists, critique mode, design QA of the built app |

## Design standards it enforces

| Standard | Applied to | Where |
|---|---|---|
| Apple Human Interface Guidelines (incl. iOS 26 Liquid Glass) | iOS chrome, type, navigation, sheets | `references/ios-hig.md` |
| Material Design 3 (incl. Expressive) | Android chrome, color roles, components | `references/material-3.md` |
| WCAG 2.2 Level AA | Everything, mapped to mobile and React Native | `references/wcag-2-2.md` |
| React Native | Buildable components, libraries, theme | `references/react-native-design.md` |

Where the guidelines differ, the stricter requirement wins (for example 44 pt / 48 dp touch targets over WCAG's 24 px minimum). Platform chrome follows each platform; content, tokens, and flows are shared.

## Repository layout

```
mobile-app-ux-design-skill/
├── README.md
├── dist/
│   └── mobile-app-ux-design.skill        # packaged skill, ready to install
└── mobile-app-ux-design/                 # the skill source
    ├── SKILL.md                          # entry point: routing table and 5-phase workflow
    ├── references/                       # loaded on demand
    │   ├── ios-hig.md                    # Apple HIG for iPhone, Liquid Glass, Dynamic Type
    │   ├── material-3.md                 # Material 3 and Expressive, color roles, components
    │   ├── wcag-2-2.md                   # WCAG 2.2 AA for mobile, RN props, test plan
    │   ├── platform-notes.md             # HIG vs M3 vs React Native mapping, pre-handoff checklist
    │   ├── react-native-design.md        # layout model, libraries, theme, performance
    │   ├── design-system.md              # how to choose tokens, roles, sizing
    │   ├── component-library.md          # ~20 core components, states, per-platform mapping
    │   ├── component-specs.md            # component and screen spec templates, states matrix
    │   ├── navigation-and-patterns.md    # navigation models, onboarding, auth, lists, forms
    │   ├── mobile-system-design.md       # offline, state, errors, performance, security, analytics
    │   ├── motion-and-haptics.md         # durations, easing, reduce motion, haptics
    │   ├── ux-writing.md                 # microcopy formulas, errors, empty states, localization
    │   ├── discovery-and-research.md     # JTBD, research methods, IA, metrics, usability tests
    │   ├── mobile-topics.md              # notifications, deep links, privacy, payments, low-end devices
    │   ├── design-qa-and-testing.md      # QA checklist, VoiceOver/TalkBack scripts, device matrix
    │   └── figma-workflow.md             # building and reviewing designs with the Figma connector
    ├── assets/
    │   ├── tokens.json                   # single source of truth for design tokens
    │   ├── mockup-template.html          # prototype base: components, 36 SVG icons, iOS/Android, light/dark, 200% text, states
    │   ├── examples/                     # working safety-critical Help/emergency SCREENS file + state menu
    │   ├── figma/                        # tested Figma snippets 01-04 + README
    │   ├── render-scenes.example.json    # example scenes for render_check.js
    │   └── evals.json                    # seven regression prompts and trigger queries
    └── scripts/
        ├── build_tokens.py               # tokens -> theme.ts, tokens.css, figma-tokens.json, contrast report
        ├── assemble_prototype.py         # builds a prototype from the template + tokens + a small SCREENS file
        ├── contrast_check.py             # WCAG contrast ratios for color pairs
        └── render_check.js               # renders a prototype in headless Chrome; reports layout problems
```

## Install

- **Claude (claude.ai / apps):** install `dist/mobile-app-ux-design.skill` through Claude's Skills settings. If you installed an older version, remove it first so only one copy is enabled.
- **Claude Code:** copy the `mobile-app-ux-design/` folder into your skills directory (for example `~/.claude/skills/`).

## Example prompts

- "Design login and onboarding for my expense tracker app. React Native, iOS and Android."
- "Review this screen of my app and tell me what to fix." (attach a screenshot or Figma link)
- "Set up a design system for my fitness app. Brand color is teal."
- "I'm building an offline-first notes app. What system decisions affect the UX?"
- "Build the checkout flow in Figma, iOS and Android."

## Tools included

**Design tokens.** Edit `assets/tokens.json`, then:

```bash
python mobile-app-ux-design/scripts/build_tokens.py mobile-app-ux-design/assets/tokens.json --out build/
```

This generates `theme.ts` (React Native), `tokens.css` (HTML mockups), `figma-tokens.json`, and a WCAG contrast report. It exits non-zero if the file is invalid or any contrast pair fails.

**Prototype render check.** After `npm install puppeteer-core @sparticuz/chromium` (or set `CHROME_PATH`):

```bash
node mobile-app-ux-design/scripts/render_check.js prototype.html scenes.json out/
```

It screenshots each scene (platform, theme, text size, state), and reports script errors, touch targets under 44 px, and horizontal overflow. Logic tests alone missed several CSS bugs.

**Contrast check.**

```bash
python mobile-app-ux-design/scripts/contrast_check.py "#15151A:#FFFFFF" "#5B5B66:#F7F7FA"
```

**Prototypes: assemble, never write from scratch.** `assets/mockup-template.html` carries the light and dark themes, iOS and Android chrome, 100%/200% text, a state switcher, 36 SVG icons, sheets, toasts, banners and buttons. You supply only a small SCREENS file:

```bash
python mobile-app-ux-design/scripts/assemble_prototype.py --tokens-css build/tokens.css \
  --screens mobile-app-ux-design/assets/examples/help-emergency.screens.js \
  --states mobile-app-ux-design/assets/examples/help-emergency.states.json --out proto.html
```

A from-scratch page tends to drop dark mode and icons (a real test produced emoji icons and no dark mode), and some accounts cap build size.

**Figma snippets.** `assets/figma/01-04` build variables (Light/Dark), text styles (SF Pro, Roboto), components with states, screens, and handoff notes through the Figma connector. See `assets/figma/README.md`.

## Verification status

What was tested:

- Token generator: valid and invalid inputs, contrast failures caught, output type-checked under strict TypeScript against stubbed React Native types.
- Mockup template: rendered in headless Chrome across platform, theme, text size, and state; logic checked with jsdom.
- Fresh-chat tests: an emergency-screen prompt triggered the skill and a REST API prompt did not. The gaps the first run showed (no dark mode, emoji icons, missing SMS and crisis-line handling) drove the assemble workflow and a safety-critical minimum bar, and the author then re-tested the patched skill in a fresh chat and confirmed it works.
- Real-world use: applied to design a health app (Today, Help and emergency, Meds and a prescription-scan flow), which drove fixes to the template, the tokens, the pattern library, and a new safety-critical section.
- Figma snippets: run in order on a blank Figma file; the variables script re-run to confirm it is idempotent; screens checked visually.
- All file references resolve; the packaged skill validates.

Not yet verified:

- The generated theme inside a full React Native project.
- Critique mode on third-party designs.

## Limitations

- It guides and drafts. It cannot run user research, replace testing with real people, or certify accessibility. It aims to *design to meet WCAG 2.2 AA*; confirm with VoiceOver, TalkBack, and real devices.
- Figma cannot show iOS 26 glass bars; the design uses a stand-in and notes that the app uses native tabs.
- Library names, OS-version behavior, and exact platform numbers change often and are marked "verify" in the references.

## Maintenance

Recheck `ios-hig.md`, `material-3.md`, `react-native-design.md`, and the "verify" items whenever a new major iOS, Android, Expo, or React Navigation version ships. Run the prompts in `assets/evals.json` after any change.

## Credits and notices

- Apple Human Interface Guidelines are Apple's; Material Design is Google's; WCAG is published by the W3C ([WCAG 2.2](https://www.w3.org/TR/WCAG22/)). The reference files summarize them in original wording and link to the sources of truth.
- This project is not affiliated with or endorsed by Apple, Google, the W3C, Figma, or Anthropic.

## License

No license has been chosen yet, so all rights are reserved by default. Add a `LICENSE` file (for example MIT) if you want others to reuse or contribute to this project.
