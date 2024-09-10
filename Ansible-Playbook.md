# Implementing an Ansible Playbook

The goal of this exercise is to configure and perform adminstrative tasks on managed hosts using a playbook.

## Things to know before getting started

Ansisble is an open-source automation platform that uses simple automation language that can accurately describe an IT application infrastructure. It is used for automating System Admin activities such as
configuration management, workflow automation,and network automation.

<p align = "center">
  <img src="https://drive.google.com/file/d/1Cg6_OISy7t3dYL_pJF8EyRNN_Gvm3mRP/view?usp=sharing">
  
* **Module:** Small piece of code with specific arguments that can act on managed hosts. Ensures that a particular part of the managed host is in a particular state
* * **Task:** A task is a fundamental unit of work that dictates how a module should be executed. Each task runs a module
* **Play:** A play performs a series of tasks in a specified order. It is often expressed in a YAML file.
* **Playbook:** A playbook is a YAML file that contains one or more plays. It provides a way to orchestrate multiple tasks across multiple hosts in a repeated manner


