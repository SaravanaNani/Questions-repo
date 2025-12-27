# Ansible Interview Questions & Answers (Deduplicated & Structured)

## 1️⃣ Ansible Fundamentals

1. **What is Ansible and why is it used?**  
Ansible is an agentless configuration management and automation tool used to provision infrastructure, configure servers, and deploy applications using YAML playbooks.

2. **What is agentless architecture in Ansible?**  
Agentless means Ansible does not require any agent on target machines. It uses SSH (Linux) or WinRM (Windows) to communicate.

3. **How does Ansible communicate with managed nodes?**  
Ansible uses SSH for Linux/Unix systems and WinRM for Windows systems.

4. **What are Ansible modules?**  
Modules are reusable units of code that perform tasks like installing packages, managing files, or controlling services.

5. **What is the difference between Ansible and shell scripts?**  
Ansible is idempotent, scalable, agentless, and declarative, while shell scripts are procedural, not idempotent, and harder to manage at scale.

6. **What is the difference between Ansible and Puppet/Chef?**  
Ansible is agentless and push-based with YAML, while Puppet/Chef are agent-based, pull-based, and use Ruby-based DSL.

---

## 2️⃣ Ansible Architecture & Core Components

7. **What is a control node in Ansible?**  
The control node is the machine where Ansible is installed and from which playbooks are executed.

8. **What are managed nodes?**  
Managed nodes are target systems that Ansible configures using modules over SSH or WinRM.

9. **What is an Ansible inventory?**  
Inventory is a file that lists managed hosts and groups of hosts.

10. **What are the types of inventory?**  
Static inventory is manually maintained, while dynamic inventory is generated automatically from cloud providers or APIs.

11. **What is an Ansible collection?**  
A collection is a packaged set of roles, modules, and plugins distributed together for reuse and versioning.

12. **What is the difference between a module and a plugin?**  
Modules perform actions on hosts, while plugins extend Ansible’s internal functionality.

---

## 3️⃣ Playbooks, Tasks & YAML

13. **What is an Ansible playbook?**  
A playbook is a YAML file that defines tasks to be executed on specific hosts.

14. **What is the structure of an Ansible playbook?**  
A playbook contains plays, each defining hosts, variables, and tasks.

15. **What is the difference between a playbook, a play, and a task?**  
A playbook contains plays, a play targets hosts, and a task is a single action.

16. **What is idempotency in Ansible?**  
Idempotency means running the same playbook multiple times produces the same result without unnecessary changes.

17. **How does Ansible ensure idempotency?**  
By using modules that check the current state before making changes.

---

## 4️⃣ Variables & Facts

18. **What are the different types of variables in Ansible?**  
Playbook vars, inventory vars, role vars, defaults, extra vars, facts, and prompted vars.

19. **What is variable precedence in Ansible?**  
Extra vars have the highest priority, role defaults have the lowest.

20. **What are Ansible facts?**  
Facts are system details automatically gathered from managed nodes.

21. **What is ansible_facts?**  
ansible_facts is a dictionary storing all gathered system information.

22. **How do you define custom facts?**  
Using set_fact, facts.d files, or role variables.

23. **What is the setup module and its use?**  
The setup module gathers system facts and stores them in ansible_facts.

---

## 5️⃣ Roles & Reusability

24. **What is an Ansible role?**  
A role is a reusable structure containing tasks, vars, templates, handlers, and files.

25. **What is the standard role directory structure?**  
tasks, handlers, vars, defaults, files, templates, meta.

26. **What is the difference between a role and a playbook?**  
A role is reusable; a playbook defines execution flow.

27. **How do you share roles across projects?**  
Using roles_path, Ansible Galaxy, or requirements.yml.

28. **What is the difference between import_role and include_role?**  
import_role loads at parse time; include_role loads dynamically at runtime.

29. **What is parse time vs runtime?**  
Parse time is playbook loading; runtime is task execution.

---

## 6️⃣ Inventory & Environment Management

30. **How do you group hosts in Ansible?**  
By defining groups in the inventory file.

31. **What are group_vars and host_vars?**  
group_vars apply to groups; host_vars apply to individual hosts.

32. **How do you manage multiple environments?**  
Using separate inventories and environment-specific variables.

33. **What is a dynamic inventory and when is it used?**  
Dynamic inventory fetches hosts from cloud APIs and is used in dynamic environments.

34. **How does Ansible fetch cloud inventories?**  
Using inventory plugins that call cloud provider APIs.

---

## 7️⃣ Handlers, Tags & Conditionals

35. **What is a handler in Ansible?**  
A handler is a task triggered only when notified by another task.

36. **When are handlers executed?**  
At the end of a play, only if notified.

37. **What are tags and why are they useful?**  
Tags allow selective execution of tasks.

38. **What is a when condition?**  
when is a conditional statement controlling task execution.

39. **What is register and how is it used?**  
register stores task output in a variable for later use.

---

## 8️⃣ Templates & Files

40. **What is Jinja2 in Ansible?**  
Jinja2 is a templating engine for dynamic configuration files.

41. **What is the difference between template and copy?**  
template supports variables; copy transfers files as-is.

42. **How do you loop in Ansible?**  
Using loop with a list of items.

43. **What is the difference between loop and with_items?**  
loop is modern and flexible; with_items is legacy.

44. **How do you manage configuration files dynamically?**  
Using Jinja2 templates with variables.

---

## 9️⃣ Security & Vault

45. **What is Ansible Vault?**  
Ansible Vault encrypts sensitive data like passwords and secrets.

46. **How do you encrypt variables?**  
Using ansible-vault encrypt or encrypt_string.

47. **How do you rotate secrets?**  
By editing vault files or rekeying the vault.

48. **Can Ansible Vault be integrated with CI/CD?**  
Yes, by injecting vault passwords securely via pipelines.

49. **Why should secrets not be stored in plain YAML?**  
Because plain YAML exposes sensitive data and violates security best practices.

---

## 🔹 Scenario-Based Ansible Interview Questions

50. **A playbook reports changes every run. Why?**  
Because tasks are not idempotent or use shell/command incorrectly.

51. **An Ansible playbook is slow. How do you optimize it?**  
Increase forks, enable pipelining, reduce fact gathering, use async.

52. **How do you structure Ansible for dev, QA, and prod?**  
Separate inventories, group_vars, reusable roles, CI/CD integration.

53. **How do you install software only on specific hosts?**  
Using inventory groups, when conditions, or host_vars.

54. **How do you restart a service only when config changes?**  
Using handlers and notify.

55. **How do you prevent secrets from appearing in logs?**  
Using no_log, Ansible Vault, and CI/CD masking.

56. **Dynamic inventory is not fetching hosts. How do you debug?**  
Check credentials, plugin config, API permissions, inventory output.

57. **Playbook fails on one host but succeeds on others. What do you do?**  
Use ignore_errors, serial, max_fail_percentage, or block/rescue.

58. **How do you deploy updates without downtime?**  
Using serial execution, health checks, and load balancer draining.

59. **How do you fix and prevent configuration drift?**  
Run playbooks periodically, ensure idempotent roles, enforce via CI/CD.

---

## 🔹 Advanced Ansible Questions

60. **What is the difference between include_tasks and import_tasks?**  
import_tasks is static; include_tasks is dynamic.

61. **What are Ansible callback plugins?**  
Plugins that react to execution events for logging or notifications.

62. **How does Ansible scale to thousands of nodes?**  
Using forks, async tasks, grouping, serial execution, and pull mode.

63. **What is Ansible Tower / AWX?**  
A web-based UI and API for managing Ansible at scale.

64. **How is RBAC implemented in Ansible Tower/AWX?**  
By assigning roles to users or teams for inventories, projects, and jobs.

65. **How do you test Ansible roles?**  
Using test playbooks, --check mode, and Molecule.

66. **What is Molecule?**  
A framework for testing Ansible roles in isolated environments.

67. **How do you manage Windows hosts in Ansible?**  
Using WinRM and Windows-specific modules (win_*).

68. **How do you debug complex Ansible playbooks?**  
Using verbosity, debug module, --check, start-at-task, and linting.

69. **How do you integrate Ansible with Terraform?**  
Terraform provisions infra, Ansible configures it using outputs or dynamic inventory.
