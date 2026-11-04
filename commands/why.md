---
description: Explain why a WhatSetter agent sent a message, from its prompt and its knowledge base, and propose the smallest fix. Changes nothing without approval.
argument-hint: "<lead name, phone or Instagram handle> [the message to explain]"
---

Explain this WhatSetter agent message: $ARGUMENTS

Follow the `whatsetter` skill, "Explain why an agent said something" playbook.

1. Find the lead (`list_conversations` or `list_leads`) and read the exchange with `get_conversation_messages`. If I didn't say which message, take the agent's last one.
2. Say which text wrote it from its `type`, then quote the sentence of the prompt (`get_agent_prompts`) or the passage of the knowledge base (`search_knowledge` with the lead's message just before it) that explains it.
3. Answer with the cause first, then the smallest change that fixes it: one sentence of the prompt, or one answer of the knowledge base, shown before and after.

Change nothing. If I approve the change, follow the "Change what an agent says" or "Change what an agent knows" playbook.
