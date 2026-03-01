# Experiment Taskset — Experiential vs Synthetic Memory

## Category 1: Information Retrieval (5 tasks)

**IR-1**: What API endpoint must be called to publish a soul on the ClawSouls website? Describe the HTTP method and required parameters.

**IR-2**: When a "Tenant or user not found" error occurs in Supabase Session Pooler, what are the causes and solutions?

**IR-3**: Explain the SoulScan scoring system. How many tiers are there, and what criteria distinguish them?

**IR-4**: What required fields were added in Soul Spec v0.4 compared to v0.3?

**IR-5**: What key exchange algorithm and symmetric encryption algorithm are used in age encryption?

## Category 2: Coding Tasks (5 tasks)

**CT-1**: When using `[name]` and `[owner]/[name]` dynamic routes at the same level in Next.js App Router, describe the problem that arises and provide a code solution.

**CT-2**: Write a Supabase RLS policy: on the `souls` table, only authenticated users can modify their own souls, and all users can read.

**CT-3**: Write a function signature and core logic for a Node.js CLI that accepts a `--format claude-code|cursor|windsurf` flag and converts a Soul Spec file to each platform's format.

**CT-4**: Write a GitHub Actions workflow: on push to main branch, build with Hugo → deploy to GitHub Pages.

**CT-5**: Write a Stripe Webhook handler in TypeScript: process the `checkout.session.completed` event and save the purchase record to the DB.

## Category 3: Architecture Decisions (5 tasks)

**AD-1**: Design an AI agent persona marketplace. When selling Souls (persona files) and Memories (experience records) as paid products, propose a payment → key distribution architecture. Include at least 2 options with pros and cons for each.

**AD-2**: You need to build a documentation site. Recommend one of Docusaurus vs GitBook vs MkDocs and explain why. Requirements: versioning, search, React component support, free hosting on GitHub Pages.

**AD-3**: Design the `sync` feature for a CLI tool. When encrypting and synchronizing a user's local memory files to a remote repository, propose an architecture. Include security, conflict resolution, and key management.

**AD-4**: You want to implement real-time validation of soul.json files in a web app. When using Monaco Editor, which approach is appropriate: inline error display within the editor vs a separate Problems panel vs both?

**AD-5**: Establish an npm package naming strategy for an open-source project. Beyond the main package `clawsouls`, you need to manage 7 packages including `soulscan`, `soulspec`, `soulhub`, `soul-spec-mcp`, etc. Include namespace strategy and squatting defense.

## Category 4: Context-Dependent Decisions (5 tasks)

**CD-1**: You need to commit to a public GitHub repo. Your organization has a policy about open-source contributions during work hours. How should you handle this?

**CD-2**: A team member (CDO) reported a bug via GitHub Issue: "JSON parsing error when creating a New Soul from the dashboard." What content should you include in your comment on the issue?

**CD-3**: A competitor (Anthropic) has released a feature similar to yours (Agent Memory). If you were to write a blog post, what tone and framing should you use?

**CD-4**: You need to decide whether to include a commercial implications section in a research paper. The paper is a Zenodo preprint, you haven't left your current company yet, and competitors exist. What judgment should you make?

**CD-5**: Your project has an opportunity for community engagement on a public forum. The account could be linked to your professional identity. Should you engage? If so, what tone?

---

## Evaluation Rubric

### Information Retrieval (IR)
- **5 pts**: Accurate and complete answer, includes details
- **4 pts**: Mostly accurate but missing some details
- **3 pts**: Right direction but contains inaccuracies
- **2 pts**: Only partially accurate
- **1 pt**: Wrong or "I don't know"

### Coding Tasks (CT)
- **5 pts**: Working code, considers edge cases, follows best practices
- **4 pts**: Working code, minor issues
- **3 pts**: Mostly correct but needs modifications
- **2 pts**: Right approach but doesn't work
- **1 pt**: Completely wrong

### Architecture Decisions (AD)
- **5 pts**: Practical, well-reasoned recommendation with trade-off analysis
- **4 pts**: Reasonable recommendation but lacks depth of analysis
- **3 pts**: Generic answer, insufficient reflection of project context
- **2 pts**: Unrealistic or lacking justification
- **1 pt**: Unable to answer or completely inappropriate

### Context-Dependent (CD)
- **5 pts**: Judgment that perfectly reflects the project situation (exit risk, team structure, competitive landscape)
- **4 pts**: Mostly appropriate but misses some context
- **3 pts**: General-level answer, doesn't reflect project-specific context
- **2 pts**: Inappropriate judgment
- **1 pt**: Dangerous judgment (ignores security or legal risks)
