Purpose:
Create learning labs for getting to know Kosli.
The target audience are DevOps,Platform engineers and software developers.
Each lab should be a markdown file by itself.

General rules:
Always search https://docs.kosli.com/, https://docs.kosli.com/tutorials/get_familiar_with_kosli/, https://kosli.com and https://docs.kosli.com/client_reference/ for information.
Always generate markdown formatted text.
For every step, if there is a corresponding cli documentation page, make a link to it.

Lab one: get ready:
* create a user on kosli.com
* fork the github repo to your own user
* see that github actions runs smooth, producing a container image, and started and stopped the application in docker.


Lab two: flows and trails
Outline: to be familiar with flow and trail.

* install the cli on your own machine or a github codespace.
* create an api key in app.kosli.com
* create a flow using the cli
* create a trail by hand
* Implement inside the workflow

Get info and inspiration from: https://docs.kosli.com/getting_started/flows/
and https://docs.kosli.com/getting_started/trails/
On the information part.



Lab three Engineering build controls Recording:
Attach attestations to:
* Artifact Binary Provenance: attest the application
* SBOM’s
* Vulnerability scans

Info: https://docs.kosli.com/getting_started/artifacts/
https://docs.kosli.com/getting_started/attestations/

Lab four: Do not implement yet.

Lab Five:
* Engineering Runtime Controls (add environment, docker)
* Recording environment changes (make snapshots)
* Adding policies (create and attach policies)

Info:
https://docs.kosli.com/getting_started/environments/
https://docs.kosli.com/getting_started/policies/