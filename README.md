# Comunication Between Two Actions
To pass the first name and second name from one action to another and display the full name in the second action's job result using the `echo` command, you can modify your workflow files as follows:

In the first action's workflow file (`my-action1.yml`):

```yaml
name: My Action 1

on:
  push:
    branches:
      - main

jobs:
  my-action1-job:
    runs-on: ubuntu-latest

    steps:
      - name: Set Output Variables
        id: set-vars
        run: |
          echo "::set-output name=first_name::John"
          echo "::set-output name=second_name::Doe"
      
      - name: Display Output
        run: |
          echo "First Name: ${{ steps.set-vars.outputs.first_name }}"
          echo "Second Name: ${{ steps.set-vars.outputs.second_name }}"
      
      - name: Trigger My Action 2
        uses: ./.github/workflows/my-action2.yml
        with:
          first_name: ${{ steps.set-vars.outputs.first_name }}
          second_name: ${{ steps.set-vars.outputs.second_name }}
```

In this workflow, the `Set Output Variables` step uses the `echo "::set-output"` command to set the values of the `first_name` and `second_name` outputs.

The `Display Output` step demonstrates how you can use the output values within the same workflow.

Finally, the `Trigger My Action 2` step triggers the second action (`my-action2.yml`) and passes the `first_name` and `second_name` values as inputs.

In the second action's workflow file (`my-action2.yml`):

```yaml
name: My Action 2

on:
  workflow_dispatch:
    inputs:
      first_name:
        description: 'First name'
        required: true
      second_name:
        description: 'Second name'
        required: true

jobs:
  my-action2-job:
    runs-on: ubuntu-latest

    steps:
      - name: Display Full Name
        run: |
          echo "Full Name: ${{ github.event.inputs.first_name }} ${{ github.event.inputs.second_name }}"
```

This workflow uses the `workflow_dispatch` event and defines two input parameters, `first_name` and `second_name`, which are required.

In the `Display Full Name` step, the `echo` command is used to display the full name by concatenating the `first_name` and `second_name` input values.

With these modifications, when you trigger the first action's workflow (`my-action1.yml`), it will set the output variables, display the output values, and trigger the second action (`my-action2.yml`) while passing the `first_name` and `second_name` values.

The second action's job will then display the full name by combining the input parameters.
