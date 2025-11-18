# Plan: Implement Kosli Learning Labs

Create five progressive learning labs to teach DevOps engineers and developers how to use Kosli for software supply chain visibility and compliance. Each lab builds upon previous concepts, starting with setup and progressing through flows, attestations, and runtime controls.
Do not go into details about the application code itself; focus on Kosli integration.

## Steps

1. **Create Lab 1: Get Ready** - Write `lab-01-get-ready.md` following `exercise-template.md` format, covering Kosli account creation, GitHub repo forking, and verifying the existing GitHub Actions workflow in `.github/workflows/full-pipeline.yaml` successfully builds and deploys the Micronaut application

2. **Create Lab 2: Flows and Trails** - Write `lab-02-flows-and-trails.md` following `exercise-template.md` format, covering Kosli CLI installation, API key creation, flow creation using `kosli create flow`, manual trail creation with `kosli begin trail`, and integration into the existing workflow scripts in `ci/` directory

3. **Create Lab 3: Build Controls and Attestations** - Write `lab-03-build-controls.md` following `exercise-template.md` format documenting artifact attestation with `kosli attest artifact`, attaching SBOM attestations (already generated via `anchore/sbom-action`), and vulnerability scan attestations using the existing Trivy scan in the workflow

4. **Create Lab 5: Runtime Controls** - Write `lab-05-runtime-controls.md` following `exercise-template.md` format covering environment creation with `kosli create environment`, Docker environment snapshotting with `kosli snapshot docker`, and policy creation/attachment using `kosli create policy` and `kosli attach-policy` for the deployed application

5. **Reference and link documentation** - For each step in all labs, add hyperlinks to corresponding CLI documentation pages from https://docs.kosli.com/client_reference/ and getting started guides from https://docs.kosli.com/getting_started/

## Further Considerations

1. **Lab 4 scope** - Should Lab 4 remain unimplemented as specified, or should it cover a specific intermediate topic like approval workflows or custom attestation types? Unimplemented for now.

2. **Exercise depth** - Should labs include troubleshooting sections and common pitfalls, or keep them minimal as step-by-step instructions only? Keep them minimal for now.
