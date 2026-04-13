# When you're stuck on a question

> 30 minutes max per question on first pass. If you're stuck:

## Stuck triage

1. **Re-read the question.** 80% of stuck moments come from misreading. Note exact wording — does it say "version" or "vulnerability"?
2. **Check the answer format.** Maybe you already have the answer but in wrong format.
3. **Have you tried `-Pn`?** Many "no response" issues = ICMP blocked.
4. **Have you tried sudo?** `-sS`, `-sU`, `-O` all need root.
5. **Different scan technique?** TCP connect (`-sT`), UDP (`-sU`), aggressive (`-A`).
6. **Rerun nmap with `-p-`** — you may have missed a non-standard port.
7. **Check the playbook** for that domain — there may be a tool you haven't tried.
8. **Background a vuln scan:** `nmap --script vuln -p- <IP> &` — keep working other questions while it runs.

## Mark and skip

If after 25 min you're still stuck:
- Note your best guess answer
- Move on
- Come back at the end if you have time
- 14/20 = pass, you don't need to solve every one

→ [time-management.md](time-management.md)
