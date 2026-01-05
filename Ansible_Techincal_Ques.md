# Ansible Interview Questions (Self-Evaluation Only)

## 1️⃣ Ansible Fundamentals
1. What is Ansible and why is it used?
2. What is agentless architecture in Ansible?
3. How does Ansible communicate with managed nodes?
4. What are Ansible modules?
5. What is the difference between Ansible and shell scripts?
6. What is the difference between Ansible and Puppet/Chef?

## 2️⃣ Ansible Architecture & Core Components
7. What is a control node in Ansible?
8. What are managed nodes?
9. What is an Ansible inventory?
10. What are the types of inventory?
11. What is an Ansible collection?
12. What is the difference between a module and a plugin?

## 3️⃣ Playbooks, Tasks & YAML
13. What is an Ansible playbook?
14. What is the structure of an Ansible playbook?
15. What is the difference between a playbook, a play, and a task?
16. What is idempotency in Ansible?
17. How does Ansible ensure idempotency?

## 4️⃣ Variables & Facts
18. What are the different types of variables in Ansible?
19. What is variable precedence in Ansible?
20. What are Ansible facts?
21. What is ansible_facts?
22. How do you define custom facts?
23. What is the setup module and its use?

## 5️⃣ Roles & Reusability
24. What is an Ansible role?
25. What is the standard role directory structure?
26. What is the difference between a role and a playbook?
27. How do you share roles across projects?
28. What is the difference between import_role and include_role?
29. What is parse time vs runtime?

## 6️⃣ Inventory & Environment Management
30. How do you group hosts in Ansible?
31. What are group_vars and host_vars?
32. How do you manage multiple environments in Ansible?
33. What is a dynamic inventory and when is it used?
34. How does Ansible fetch cloud inventories?

## 7️⃣ Handlers, Tags & Conditionals
35. What is a handler in Ansible?
36. When are handlers executed?
37. What are tags and why are they useful?
38. What is a when condition?
39. What is register and how is it used?

## 8️⃣ Templates & Files
40. What is Jinja2 in Ansible?
41. What is the difference between template and copy?
42. How do you loop in Ansible?
43. What is the difference between loop and with_items?
44. How do you manage configuration files dynamically?

## 9️⃣ Security & Vault
45. What is Ansible Vault?
46. How do you encrypt variables?
47. How do you rotate secrets?
48. Can Ansible Vault be integrated with CI/CD?
49. Why should secrets not be stored in plain YAML?

## 🔹 Scenario-Based Ansible Questions
50. A playbook reports changes every run. Why?
51. An Ansible playbook is slow. How do you optimize it?
52. How do you structure Ansible for dev, QA, and prod?
53. How do you install software only on specific hosts?
54. How do you restart a service only when config changes?
55. How do you prevent secrets from
