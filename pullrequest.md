feat(QTree,a11y): Added aria roles, tags, and keyboard interactions #4068

**What kind of change does this PR introduce?** (check at least one)

- [ ] Bugfix
- [x] Feature
- [ ] Documentation
- [ ] Code style update
- [ ] Refactor
- [ ] Build-related changes
- [ ] Other, please describe:

**Does this PR introduce a breaking change?** (check one)

- [ ] Yes
- [x] No

If yes, please describe the impact and migration path for existing applications:

**The PR fulfills these requirements:**

- [x] It's submitted to the `dev` branch and _not_ the `master` branch
- [x] When resolving a specific issue, it's referenced in the PR's title (e.g. `fix: #xxx[,#xxx]`, where "xxx" is the issue number)
- [ ] It's been tested on a Cordova (iOS, Android) app
- [ ] It's been tested on a Electron app
- [ ] Any necessary documentation has been added or updated [in the docs](https://github.com/quasarframework/quasar/tree/dev/docs) (for faster update click on "Suggest an edit on GitHub" at bottom of page) or explained in the PR's description.

If adding a **new feature**, the PR's description includes:
- [x] A convincing reason for adding this feature (to avoid wasting your time, it's best to open a suggestion issue first and wait for approval before working on it)

**Other information:**

**This is not ready yet in terms of having been tested in Cordova and Electron or documentation updates, but I need some feedback and direction on a few things before I can finish .**

- This pull request adds the relevant ARIA roles and tags for the tree/treeitem to QTree as specified in [WAI-ARIA 1.1](https://www.w3.org/TR/wai-aria-1.1/#tree)
- This pull requests also (optionally) adds keyboard interactions to QTree that attempt to be consistent with [WAI-ARIA Authoring Practices 1.1 section 3.25 - Tree View](https://www.w3.org/TR/wai-aria-practices-1.1/#TreeView) and [section 6 - Developing a Keyboard Interface] (https://www.w3.org/TR/wai-aria-practices-1.1/#keyboard). In particular:
  - boolean property _arrowNavigation_ enables the new navigation mode (to allow this to be a non-breaking change)
  - Synchronizable string property _cursor_ specifies key of the initial cursor node updates as movement occurs. If cursor is not initially specified it will be set to the key if the first node (if any)
  - With arrow navigation enabled
    - only the cursor node is in the tab order, thus tabbing from the focused item just preceding the tree will focus the cursor node, and tabbing from there will move focus out of the tree to the next item in the tab order
    - the cursor can be moved within the tree by clicking a node, using the up/down arrow, Home, and End keys
    - the left/right arrow keys collapse/expand parent items or move up or down a level, depending on the type and state of the cursor tree item
    - the cursor can be moved to disabled items (because otherwise they would be undiscoverable
    - space key will toggle the ticked state of the cursor node if ticking is enabled and the node is tickable
    - enter key will toggle the selected state of the cursor node
  - the cursor node is styled using the q-manual-focusable--focused whether or not it has the focus

Feedback/guidance is needed on the following:

1. Should this be something that has to be enabled or would it be reasonable to do away with the old navigational model where all eligible nodes and checkboxes were in the tab order, or at least make arrow key key navigation the new default.
2. If the new navigation should not be made the default, what should the property name really be called? I don't love _arrowNavigation_, I considered _ariaNavigation_, but really this style of navigation pre-dates ARIA by 3 decades. Suggestions on something that would fit with other quasar terminology?
3. QTree implements an odd hybrid selection model with independent models for single and multi select that can both be enabled at the same time. This gets even more complicated with the notion of the cursor, which is, in a way a third selection model. ARIA specifies how each of those modes should act alone, but having them both available at the same time leaves all kinds of questions about how the keyboard selection model should work. At this point space is always used for ticking and enter is always used for selecting, but I would almost thing that if only one selection mode were enabled, they both might do the same thing, but I think that this difference in behavior would be confusing those dependent on a screen reader. Any thoughts about this? There are also the shift and ctrl key combinations along with arrow keys that could be implemented, but become really muddy in the face of the existing selection models.
4. There really needs to be a visual difference between the cursor node when it has focus and the cursor node when it doesn't have focus. I've thought about a traditional focus ring around the cursor item or perhaps around the whole tree, but that doesn't really seem to fit   
