# Lab-Actions
GH Actions lab

How does GitHub Actions automate development tasks?
Completed
100 XP
10 minutes
Here, we'll introduce GitHub Actions and workflows. You'll learn the types of actions you can use and where to find them. You'll also look at examples of these types of actions and how they fit in a workflow.

GitHub decreases time from idea to deployment
GitHub is designed to help teams of developers and DevOps engineers build and deploy applications quickly. There are many features in GitHub that enable this, but they generally fall into one of two categories:

Communication: Consider all of the ways that GitHub makes it easy for a team of developers to communicate about the software development project: code reviews in pull requests, GitHub issues, project boards, wikis, notifications, and so on.
Automation: GitHub Actions lets your team automate workflows at every step in the software-development process, from integration to delivery to deployment. It even lets you automate adding labels to pull requests and checking for stale issues and pull requests.
When combined, these features have allowed thousands of development teams to effectively decrease the amount of time it takes from their initial idea to deployment.

Use workflow automation to decrease development time
We'll focus on automation in this module. Let's take a moment to understand how teams can use automation to reduce the amount of time it takes to complete a typical development and deployment workflow.

Consider all of the tasks that must happen after the code is written, but before you can reliably use the code for its intended purpose. Depending on your organization's goals, you'll likely need to perform one or more of the following tasks:

Ensure the code passes all unit tests.
Perform code quality and compliance checks to make sure the source code meets the organization's standards.
Check the code and its dependencies for known security issues.
Build the code integrating new source from (potentially) multiple contributors.
Ensure the software passes integration tests.
Version the new build.
Deliver the new binaries to the appropriate filesystem location.
Deploy the new binaries to one or more servers.
If any of these tasks don't pass, report the issue to the proper individual or team for resolution.
The challenge is to do these tasks reliably, consistently, and in a sustainable manner. This is an ideal job for workflow automation. If you're already relying on GitHub, you'll likely want to set up your workflow automation using GitHub Actions.

What is GitHub Actions?
GitHub Actions are packaged scripts to automate tasks in a software-development workflow in GitHub. You can configure GitHub Actions to trigger complex workflows that meet your organization's needs. The trigger can happen each time developers check new source code into a specific branch, at timed intervals, or manually. The result is a reliable and sustainable automated workflow, which leads to a significant decrease in development time.

Where can you find GitHub Actions?
GitHub Actions are scripts that adhere to a yml data format. Each repository has an Actions tab that provides a quick and easy way to get started with setting up your first script. If you see a workflow that you think might be a great starting point, just select the Configure button to add the script and begin editing the source yml.

Screenshot of the *Actions tab* in GitHub Actions displaying a simple workflow and a button to set up this workflow.

However, beyond those GitHub Actions featured on the Actions tab, you can:

Search for GitHub Actions in the GitHub Marketplace. The GitHub Marketplace allows you to discover and purchase tools that extend your workflow.
Search for open-source projects. For example, the GitHub Actions organization features many popular open-source repos containing GitHub Actions you can use.
Write your own GitHub Actions from scratch. You can make them open source, or even publish them to the GitHub Marketplace.
Using open-source GitHub Actions
Many GitHub Actions are open source and available for anyone who wants to use them. However, just like with any open-source software, you need to carefully check them before using them in your project. Similar to recommended community standards with open-source software such as including a README, code of conduct, contributing file, and issue templates, you can follow these recommendations when using GitHub Actions:

Review the action's action.yml file for inputs, outputs, and to make sure the code does what it says it does.
Check if the action is in the GitHub Marketplace. This is a good check, even if an action doesn't have to be on the GitHub Marketplace to be valid.
Check if the action is verified in the GitHub Marketplace. This means that GitHub has approved the use of this action. However, you should still review it before using it.
Include the version of the action you're using by specifying a Git ref, SHA, or tag.
Types of GitHub actions
There are three types of GitHub actions: container actions, JavaScript actions, and composite actions.

With container actions, the environment is part of the action's code. These actions can only be run in a Linux environment that GitHub hosts. Container actions support many different languages.

JavaScript actions don't include the environment in the code. You'll have to specify the environment to execute these actions. You can run these actions in a VM (virtual machine) in the cloud or on-premises. JavaScript actions support Linux, macOS, and Windows environments.

Composite actions allow you to combine multiple workflow steps within one action. For example, you can use this feature to bundle together multiple run commands into an action, and then have a workflow that executes the bundled commands as a single step using that action.

Sample Workflow Using a Composite Action
Here's a complete example showing how to create and use a composite action:

1. First, create the composite action file
Create a file at .github/actions/setup-environment/action.yml:

yaml
Copy
Download
name: 'Setup Python Environment'
description: 'Sets up Python, installs dependencies, and caches them'
inputs:
  python-version:
    description: 'Version of Python to use'
    required: true
    default: '3.10'
  dependencies-file:
    description: 'Path to requirements file'
    required: true
    default: 'requirements.txt'

runs:
  using: "composite"
  steps:
    - name: Set up Python ${{ inputs.python-version }}
      uses: actions/setup-python@v4
      with:
        python-version: ${{ inputs.python-version }}
    
    - name: Cache pip dependencies
      uses: actions/cache@v3
      with:
        path: ~/.cache/pip
        key: ${{ runner.os }}-pip-${{ hashFiles(inputs.dependencies-file) }}
        restore-keys: |
          ${{ runner.os }}-pip-
    
    - name: Install dependencies
      run: pip install -r ${{ inputs.dependencies-file }}
      shell: bash
2. Then, create a workflow that uses this composite action
Create a file at .github/workflows/test.yml:

yaml
Copy
Download
name: CI Pipeline

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
        
      - name: Setup Python environment
        uses: ./.github/actions/setup-environment
        with:
          python-version: '3.10'
          dependencies-file: 'requirements.txt'
          
      - name: Run tests
        run: pytest
        shell: bash

The anatomy of a GitHub action
Here's an example of an action that performs a git checkout of a repository. This action, actions/checkout@v1, is part of a step in a workflow. This step also builds the Node.js code that was checked out. We'll talk about workflows, jobs, and steps in the next section.

yml

Copy
steps:
  - uses: actions/checkout@v1
  - name: npm install and build webpack
    run: |
      npm install
      npm run build
Suppose you want to use a container action to run containerized code. Your action might look like this:

yml

Copy
name: "Hello Actions"
description: "Greet someone"
author: "octocat@github.com"

inputs:
    MY_NAME:
      description: "Who to greet"
      required: true
      default: "World"

runs:
    uses: "docker"
    image: "Dockerfile"

branding:
    icon: "mic"
    color: "purple"
Notice the inputs section. Here, you're getting the value of a variable called MY_NAME. This variable will be set in the workflow that runs this action.

In the runs section, notice you specify docker in the uses attribute. When you do this, you'll need to provide the path to the Docker image file. Here, it's called Dockerfile. We won't get into the specifics of Docker here, but if you'd like more information, check out the Introduction to Docker Containers module.

The last section, branding, personalizes your action in the GitHub Marketplace if you decide to publish it there.

You can find a complete list of action metadata at Metadata syntax for GitHub Actions.

What is a GitHub Actions workflow?
A GitHub Actions workflow is a process that you set up in your repository to automate software-development lifecycle tasks, including GitHub Actions. With a workflow, you can build, test, package, release, and deploy any project on GitHub.

To create a workflow, you add actions to a .yml file in the .github/workflows directory in your GitHub repository.

In the exercise coming up, your workflow file main.yml will look like this:

yml

Copy
name: A workflow for my Hello World file
on: push
jobs:
  build:
    name: Hello world action
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v1
    - uses: ./action-a
      with:
        MY_NAME: "Mona"
Notice the on: attribute. This is a trigger to specify when this workflow will run. Here, it triggers a run when there's a push event to your repository. You can specify single events like on: push, an array of events like on: [push, pull_request], or an event-configuration map that schedules a workflow or restricts the execution of a workflow to specific files, tags, or branch changes. The map might look something like this:

yml

Copy
on:
  # Trigger the workflow on push or pull request,
  # but only for the main branch
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
  # Also trigger on page_build, as well as release created events
  page_build:
  release:
    types: # This configuration doesn't affect the page_build event above
      - created
An event will trigger on all activity types for the event unless you specify the type or types. For a comprehensive list of events and their activity types, see: Events that trigger workflows in the GitHub documentation.

A workflow must have at least one job. A job is a section of the workflow associated with a runner. A runner can be GitHub-hosted or self-hosted, and the job can run on a machine or in a container. You'll specify the runner with the runs-on: attribute. Here, you're telling the workflow to run this job on ubuntu-latest.

Each job will have steps to complete. In our example, the step uses the action actions/checkout@v1 to check out the repository. What's interesting is the uses: ./action-a value, which is the path to the container action that you build in an action.yml file.

The last part of this workflow file sets the MY_NAME variable value for this workflow. Recall the container action took an input called MY_NAME.

For more information on workflow syntax, see Workflow syntax for GitHub Actions

GitHub-hosted versus self-hosted runners
We briefly mentioned runners as being associated with a job. A runner is simply a server that has the GitHub Actions runner application installed. In the previous workflow example, there was a runs-on: ubuntu-latest attribute within the jobs block, which told the workflow that the job will run using the GitHub-hosted runner that's running in the ubuntu-latest environment.

When it comes to runners, there are two options from which to choose: GitHub-hosted runners or self-hosted runners. If you use a GitHub-hosted runner, each job runs in a fresh instance of a virtual environment. The GitHub-hosted runner type you define, runs-on: {operating system-version} then specifies that environment. With self-hosted runners, you need to apply the self-hosted label, its operating system, and the system architecture. For example, a self-hosted runner with a Linux operating system and ARM32 architecture would look like the following: runs-on: [self-hosted, linux, ARM32].

Each type of runner has its benefits, but GitHub-hosted runners offer a quicker and simpler way to run your workflows, albeit with limited options. Self-hosted runners are a highly configurable way to run workflows in your own custom local environment. You can run self-hosted runners on-premises or in the cloud. You can also use self-hosted runners to create a custom hardware configuration with more processing power or memory. This will help to run larger jobs, install software available on your local network, and choose an operating system not offered by GitHub-hosted runners.

GitHub Actions may have usage limits
GitHub Actions has some usage limits, depending on your GitHub plan and whether your runner is GitHub-hosted or self-hosted. For more information on usage limits, check out Usage limits, billing, and administration in the GitHub documentation.

GitHub hosted larger runners
GitHub offers larger runners for workflows that require more resources. These runners are GitHub-hosted and provide increased CPU, memory, and disk space compared to standard runners. They are designed to handle resource-intensive workflows efficiently, ensuring optimal performance for demanding tasks.

Runner sizes and labels
Larger runners are available in multiple configurations, providing enhanced vCPUs, RAM, and SSD storage to meet diverse workflow requirements. These configurations are ideal for scenarios such as:

Compiling large codebases with extensive source files.
Running comprehensive test suites, including integration and end-to-end tests.
Processing large datasets for data analysis or machine learning tasks.
Building applications with complex dependencies or large binary outputs.
Performing high-performance simulations or computational modeling.
Executing video encoding, rendering, or other multimedia processing workflows.
To use a larger runner, specify the desired runner label in the runs-on attribute of your workflow file. For example, to use a runner with 16 vCPUs and 64 GB of RAM, you would set runs-on: ubuntu-latest-16core.

yml

Copy
jobs:
  build:
    runs-on: ubuntu-latest-16core
    steps:
      - uses: actions/checkout@v2
      - name: Build project
        run: make build
These larger runners maintain compatibility with existing workflows by including the same preinstalled tools as standard ubuntu-latest runners.

For more details on runner sizes for larger runners, refer to the GitHub documentation [https://docs.github.com/en/actions/using-github-hosted-runners/using-larger-runners/about-larger-runners#machine-sizes-for-larger-runners]

Managing larger runners
GitHub provides tools to manage larger runners effectively, ensuring optimal resource utilization and cost management. Here are some key aspects of managing larger runners:

Monitoring usage
You can monitor the usage of larger runners through the GitHub Actions usage page in your repository or organization settings. This page provides insights into the number of jobs run, the total runtime, and the associated costs.

Managing access
To control access to larger runners, you can configure repository or organization-level policies. This ensures that only authorized workflows or teams can use these high-resource runners.

Cost management
Larger runners incur additional costs based on their usage. To manage costs, consider the following:

Use larger runners only for workflows that require high resources.
Optimize workflows to reduce runtime.
Monitor billing details regularly to track expenses.
Scaling workflows
If your workflows require frequent use of larger runners, consider scaling strategies such as:

Using self-hosted runners for predictable workloads.
Splitting workflows into smaller jobs to distribute the load across standard runners.

Identify the components of GitHub Actions
Completed
100 XP
3 minutes
Here, you'll learn about the basic components of a GitHub Actions workflow file.
![image](https://github.com/user-attachments/assets/1fe4d939-45ce-42c0-bd83-00ab42a5d810)

The components of GitHub Actions
Screenshot of a GitHub Actions workflow file showing the job, step, and action components.

There are several components that work together to run tasks or jobs within a GitHub Actions workflow. In short, an event triggers the workflow, which contains a job. This job then uses steps to dictate which actions will run within the workflow. To better see how these components work together, let's take a quick look at each one.

Workflows
A workflow is an automated process that you add to your repository. A workflow needs to have at least one job, and different events can trigger it. You can use it to build, test, package, release, or deploy your repository's project on GitHub.

Jobs
The job is the first major component within the workflow. A job is a section of the workflow that will be associated with a runner. A runner can be GitHub-hosted or self-hosted, and the job can run on a machine or in a container. You'll specify the runner with the runs-on: attribute. Here, you're telling the workflow to run this job on ubuntu-latest. We'll talk more about runners in the next unit.

Steps
A step is an individual task that can run commands in a job. In our preceding example, the step uses the action actions/checkout@v2 to check out the repository. What's interesting is the uses: ./action-a value. This is the path to the container action that you'll build in an action.yml file.

Actions
The actions inside your workflow are the standalone commands that are executed. These standalone commands can reference GitHub actions such as using your own custom actions, or community actions like the one we use in the preceding example, actions/checkout@v2. You can also run commands such as run: npm install -g bats to execute a command on the runner.
