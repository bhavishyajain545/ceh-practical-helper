# First contact — moment you read a question

> The first 30 seconds of every CEH Practical question.

1. **Read the question twice.** Note: target IP(s), what's being asked for, expected answer format.
2. **Note the answer format.** "version", "CVE-XXXX-XXXX", integer, hostname, etc.
3. **Open a fresh terminal tab.** Background a full nmap immediately:
   ```bash
   nmap -sV -sC -p- -T4 -oA bg <IP> &
   ```
4. **Open this repo's [master playbook](../playbooks/master-playbook.md)** and Ctrl+F a keyword from the question.
5. **Pick your tool. Open its doc in a new Firefox tab.** Copy commands from there.

→ Then [first-5-commands.md](first-5-commands.md) for the commands themselves.
