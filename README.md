# 📜 GitHub Action: Validate Deadline for Push and PRs

This GitHub Action **blocks any push or pull request** that occurs after a specified deadline date and time. Currently, the action **only allows push and PRs before 11 PM (Bogotá) on March 20, 2025**.

## 🚀 How Does It Work?
1. The action is triggered on any `push` or `pull_request`.
2. It retrieves the current date and time in Bogotá (UTC-5 time zone).
3. It compares the current date with the defined deadline.
4. If the current date **is later than the deadline or the time is 11 PM or later**, the `push` or `PR` will be blocked with an error.

## 📂 Workflow Configuration
The action file is located at:
```
.github/workflows/validate-push-deadline.yml
```

### 🛠️ How to Change the Deadline?

```yaml
deadline_date="2025-03-20"  # Change the deadline date (YYYY-MM-DD)
deadline_hour=23            # Change the deadline hour in 24-hour format (e.g., 22 for 10 PM)
```

For example, if you want the new deadline to be **April 15, 2025, at 9 PM**, update these lines to:
```yaml
deadline_date="2025-04-15"
deadline_hour=21
```

## 🔒 Branch Protection Configuration
If you want to prevent `merge` into branches like `develop` or `master` after the deadline, follow these steps:
1. Go to your repository in **GitHub** → **Settings** → **Branches**.
2. Under **Branch protection rules**, select `develop` and `master`.
3. Enable **Require status checks to pass before merging**.
4. Select the action `Validate Push Deadline` as a required check.
5. Save the changes.

## ✅ Expected Outcome
- **Before the deadline (March 20, 2025, 11 PM Bogotá)**: `Push` and `PRs` are allowed.
- **After the deadline**: Any `push` or `PR` will be rejected with an error.

---

## Ruleset

Create a new branch rule set to block push and merge if deadline is not valid, like this:

```json
{
  "id": 4192410,
  "name": "develop",
  "target": "branch",
  "source_type": "Repository",
  "source": "camilonfs1/test-rulesets",
  "enforcement": "active",
  "conditions": {
    "ref_name": {
      "exclude": [],
      "include": [
        "refs/heads/develop",
        "refs/heads/main"
      ]
    }
  },
  "rules": [
    {
      "type": "deletion"
    },
    {
      "type": "non_fast_forward"
    },
    {
      "type": "required_status_checks",
      "parameters": {
        "strict_required_status_checks_policy": false,
        "do_not_enforce_on_create": false,
        "required_status_checks": [
          {
            "context": "check-deadline",
            "integration_id": 15368
          }
        ]
      }
    },
    {
      "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 1,
        "dismiss_stale_reviews_on_push": false,
        "require_code_owner_review": false,
        "require_last_push_approval": false,
        "required_review_thread_resolution": false,
        "allowed_merge_methods": [
          "merge",
          "squash",
          "rebase"
        ]
      }
    }
  ],
  "bypass_actors": [
    {
      "actor_id": 5,
      "actor_type": "RepositoryRole",
      "bypass_mode": "always"
    }
  ]
}
```