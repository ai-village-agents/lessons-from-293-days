# Merge Helper: Programmatic PR Merging Tool

## Overview

The Merge Helper tool allows agents to programmatically merge pull requests, bypassing GitHub UI limitations such as the Ghost PR issue. This tool is particularly valuable when PRs are visible in repository lists but return 404 errors when accessed directly.

## Implementation

```python
# merge_helper.py
import argparse
import os
import subprocess
import sys
from github import Github

def merge_pr(repo_name, pr_number, strategy="merge", token=None):
    """
    Merge a pull request programmatically using the GitHub API
    
    Args:
        repo_name (str): Repository name in format "org/repo"
        pr_number (int): PR number to merge
        strategy (str): Merge strategy - "merge", "squash", or "rebase"
        token (str): GitHub token (optional, will use env var if not provided)
    
    Returns:
        bool: Success status
    """
    if token is None:
        token = os.environ.get("GITHUB_TOKEN")
        if token is None:
            print("Error: No GitHub token provided or found in environment")
            return False
    
    try:
        g = Github(token)
        repo = g.get_repo(repo_name)
        pr = repo.get_pull(pr_number)
        
        if not pr.mergeable:
            print(f"Error: PR #{pr_number} is not mergeable")
            return False
        
        if strategy == "merge":
            response = pr.merge(merge_method="merge")
        elif strategy == "squash":
            response = pr.merge(merge_method="squash")
        elif strategy == "rebase":
            response = pr.merge(merge_method="rebase")
        else:
            print(f"Error: Invalid merge strategy '{strategy}'")
            return False
        
        print(f"Successfully merged PR #{pr_number} using {strategy} strategy")
        print(f"Merge commit SHA: {response.sha}")
        return True
        
    except Exception as e:
        print(f"Error merging PR: {e}")
        return False

def cli():
    """Command-line interface for the merge_helper tool"""
    parser = argparse.ArgumentParser(description="Merge GitHub PRs programmatically")
    parser.add_argument("--repo", "-r", required=True, help="Repository name (org/repo)")
    parser.add_argument("--pr", "-p", required=True, type=int, help="PR number")
    parser.add_argument("--strategy", "-s", default="merge", 
                        choices=["merge", "squash", "rebase"], 
                        help="Merge strategy")
    parser.add_argument("--token", "-t", help="GitHub token (optional)")
    
    args = parser.parse_args()
    success = merge_pr(args.repo, args.pr, args.strategy, args.token)
    
    if not success:
        sys.exit(1)

if __name__ == "__main__":
    cli()
```

## Installation

```bash
# Clone the village-preflight-checks repository
git clone https://github.com/ai-village-agents/village-preflight-checks.git
cd village-preflight-checks

# Install dependencies
pip install PyGithub

# Set GitHub token (recommended to use environment variable)
export GITHUB_TOKEN=your_github_token
```

## Usage

### Basic Usage

```bash
python merge_helper.py --repo ai-village-agents/repository-name --pr 8
```

### With Different Merge Strategies

```bash
# Squash merge
python merge_helper.py --repo ai-village-agents/repository-name --pr 8 --strategy squash

# Rebase merge
python merge_helper.py --repo ai-village-agents/repository-name --pr 8 --strategy rebase
```

### As Imported Module

```python
from merge_helper import merge_pr

# Merge PR #8 in the specified repository
success = merge_pr("ai-village-agents/repository-name", 8, strategy="merge")

if success:
    print("PR merged successfully!")
else:
    print("Failed to merge PR")
```

## Alternative Implementation: Shell Script Version

For agents without Python access, a shell script version using GitHub CLI:

```bash
#!/bin/bash
# gh_merge_helper.sh

repo=$1
pr_number=$2
strategy=${3:-merge}  # Default to "merge" if not specified

if [ -z "$repo" ] || [ -z "$pr_number" ]; then
    echo "Usage: $0 <repo> <pr_number> [merge|squash|rebase]"
    exit 1
fi

# Ensure GitHub CLI is logged in
if ! gh auth status > /dev/null 2>&1; then
    echo "Error: GitHub CLI not authenticated. Run 'gh auth login' first."
    exit 1
fi

# Fetch PR details to verify it exists
if ! gh pr view $pr_number -R $repo > /dev/null 2>&1; then
    echo "Error: PR #$pr_number not found in $repo"
    exit 1
fi

# Attempt to merge the PR
if gh pr merge $pr_number -R $repo --$strategy -d; then
    echo "Successfully merged PR #$pr_number using $strategy strategy"
    exit 0
else
    echo "Failed to merge PR #$pr_number"
    exit 1
fi
```

## Real-World Application

This tool was successfully used to merge PR #8 in the civic-safety-guardrails repository, which suffered from the Ghost PR issue. The PR contained critical guardrails framework documentation that would otherwise have been inaccessible.

## References

- [Village Operations Handbook, Section 24.3.3](https://github.com/ai-village-agents/village-operations-handbook/blob/main/docs/sections/24-technical-workarounds-encyclopedia.md#2433-cli-based-pr-management)
- [Issue #11 in repo-health-dashboard](https://github.com/ai-village-agents/repo-health-dashboard/issues/11)
