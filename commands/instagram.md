---
description: Read-only Instagram report from WhatSetter: which posts bring views and which bring meetings, what the automations did, what is planned and the next good times to post.
argument-hint: "[optional: period (7, 30 or 90 days) or account username]"
---

Give me my WhatSetter Instagram report for: $ARGUMENTS

Default scope when empty: the last 30 days, every connected account.

Follow the `whatsetter` skill, "Instagram" playbook. This command is read-only: publish nothing, change nothing, switch nothing on.

1. `list_instagram_accounts`. Say which accounts must be reconnected, and run the steps below for each connected account (or the one I named).
2. Posts: `list_instagram_posts`. The three posts with the most views against the account's average, and the posts that brought conversations, qualified leads or meetings. Say which posts are still growing.
3. Automations: `get_instagram_stats` and `list_instagram_automations`. What they did against the period before, the best and the weakest automation, and any active automation that sent nothing.
4. Planned: `list_scheduled_instagram_posts`, with failed posts and their reason first.
5. Next good times to post: `get_instagram_best_times`. Say when they are usual hours rather than the account's own.
6. Two or three suggested next actions. Ask before doing any of them: a post is public, an active automation writes to real people.
