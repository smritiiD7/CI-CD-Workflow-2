# GitHub Actions — Mini Guide / README

---

## 1. What is GitHub Actions?

GitHub Actions allows you to **automate CI/CD workflows** directly in your GitHub repository using YAML configuration files stored in the `.github/workflows/` folder.

---

## 2. Basic Terminologies & Reserved vs Non-Reserved Keywords

### a) Reserved Keywords (must be used as-is):

| Reserved Keywords   | Purpose                                                        |
| ------------------- | -------------------------------------------------------------- |
| `name`              | Name of the workflow                                           |
| `on`                | Specifies events that trigger the workflow                     |
| `jobs`              | Groups all jobs                                                |
| `runs-on`           | OS runner (like `ubuntu-latest`)                               |
| `steps`             | Defines list of commands to run in a job                       |
| `uses`              | Uses an action from GitHub Marketplace                         |
| `run`               | Direct shell commands                                          |
| `with`              | Passes parameters to an action                                 |
| `needs`             | Creates dependency on other jobs                               |
| `workflow_dispatch` | Manual trigger declaration (for manually running the workflow) |

### b) Non-Reserved (User-Defined) Keywords:

| Non-Reserved                                    | Meaning                                  |
| ----------------------------------------------- | ---------------------------------------- |
| Workflow Name (`name`)                          | Any string (e.g., "Test Project")        |
| Job IDs (`test`, `deploy`)                      | Job names can be anything you choose     |
| Step names (`Get Code`, `Install dependencies`) | Purely for human readability             |
| Version (`@v3`, `@v4`)                          | Action versions, changeable              |
| Input values (`node-version: '18'`)             | Changeable depending on your requirement |

---

## 3. Example — GitHub Actions Workflow Explained

```yaml
name: Test Project   # Non-reserved (Workflow Name)

on: push            # Reserved - Event Triggered when code is pushed

jobs:              # Reserved

  test:            # Non-reserved (Job ID - you can rename it)
    runs-on: ubuntu-latest   # Reserved
    steps:        # Reserved
      - name: Get Code      # Non-reserved (Step name)
        uses: actions/checkout@v3    # Reserved
      - name: Install NodeJS
        uses: actions/setup-node@v3
        with:             # Reserved
          node-version: '18'    # Non-reserved (Value)

      - name: Install dependencies
        run: npm install   # Reserved (run)

      - name: Run tests
        run: npm test
```

---

## 4. workflow\_dispatch — Manual Workflow Trigger

If you want to **manually trigger a workflow** from GitHub’s UI:

```yaml
on:
  workflow_dispatch:    # Reserved
```

You can also accept **input parameters**:

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Choose the environment'
        required: true
        default: 'production'
```

This will show a "Run workflow" button on GitHub where you can select the environment.

---

## 5. Job Dependency using `needs`

To run jobs in **a particular order**:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Run tests
        run: npm test

  deploy:
    needs: test    # Reserved keyword - waits for 'test' job
    runs-on: ubuntu-latest
    steps:
      - name: Deploy step
        run: echo "Deploying after successful test"
```

Here, the `deploy` job will only run after the `test` job finishes successfully.

---

## 6. Checking Repo Files on Runner Machine

To see how your repo is downloaded on the GitHub-hosted machine:

```yaml
steps:
  - name: Before Checkout
    run: ls -a

  - name: Checkout Repo
    uses: actions/checkout@v4

  - name: After Checkout
    run: ls -a
```

---

## 7. Important Notes:

✅ **Job ID (like `test`, `deploy`)** — customizable (user-defined)
✅ **Runner Image (`ubuntu-latest`)** — can be changed to `windows-latest`, etc.
✅ **Step Names (`Get Code`, `Install NodeJS`)** — purely descriptive, not functional
✅ **Event Triggers (`push`, `pull_request`)** — predefined, not customizable
✅ **Actions (`actions/checkout`, `actions/setup-node`)** — must use correct names from GitHub Marketplace
✅ **Manual trigger with `workflow_dispatch`** — lets you run workflows on demand

---

## 8. Example — Manual + Push Trigger Combined

```yaml
on:
  push:           # Trigger on push
  workflow_dispatch:    # Manual trigger as well
```

---

## Summary Table: Reserved vs Non-Reserved

| **Reserved (Cannot Change)**                                                          | **Non-Reserved (Customizable)**                                   |
| ------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `on`, `jobs`, `steps`, `uses`, `run`, `with`, `needs`, `runs-on`, `workflow_dispatch` | Job IDs, Workflow name, Step names, Input values, Action versions |

---

