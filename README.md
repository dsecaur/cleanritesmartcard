# cleanritesmartcardname:
Run Behaviour Driven Development Tests on:
  workflow_dispatch:
    inputs:
      ref:
        description: "The branch, tag or SHA to checkout"
        required: true
        default: "develop"

jobs:
  setup_behave:
    name: "Build and Run Behave"
    runs-on: ubuntu-20.04
    if: github.repository_owner == 'bcgov'
    steps:
      - uses: actions/checkout@v2
        with:
          ref: ${{ github.event.inputs.ref }}
      
      - name: Set up Python 3.10
        uses: actions/setup-python@v2
        with:
          python-version: "3.10"
          
      - name: Install dependencies
        shell: bash      
        run: |
          cd services/traction/bdd-tests
          python -m pip install --upgrade pip
          python -m pip install -r ./requirements.txt
      - name: run behave
        shell: bash      
        run: |
          cd services/traction/bdd-tests
          behave --define traction_host=${{secrets.BDD_TRACTION_HOST}} --define innkeeper_password=${{secrets.BDD_TRACTION_INNKEEPER_PWD}}
