# Google Kubernetes Engine Security: Binary Authorization

1 hour 30 minutes 7 Credits

![GKE-Engine.png](https://cdn.qwiklabs.com/QKV092kALeUu8E6Qu3qVrHWJ%2BjkMkSlR%2BwZpsbfX73w%3D)

## GSP479

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

One of the key security concerns for running Kubernetes clusters is knowing what container images are running inside each pod and being able to account for their origin. Establishing "container provenance" means having the ability to trace the source of a container to a trusted point of origin and ensuring your organization follows the desired processes during artifact (container) creation.

Some of the key concerns are:

*   **Safe Origin** - How do you ensure that all container images running in the cluster come from an approved source?
*   **Consistency and Validation** - How do you ensure that all desired validation steps were completed successfully for every container build and every deployment?
*   **Integrity** - How do you ensure that containers were not modified before running after their provenance was proven?

From a security standpoint, not enforcing where images originate from presents several risks:

*   A malicious actor that has compromised a container may be able to obtain sufficient cluster privileges to launch other containers from an unknown source without enforcement.
*   An authorized user with the permissions to create pods may be able to accidentally or maliciously run an undesired container directly inside a cluster.
*   An authorized user may accidentally or maliciously overwrite a docker image tag with a functional container that has undesired code silently added to it, and Kubernetes will pull and deploy that container as a part of a deployment automatically.

To help system operators address these concerns, Google Cloud Platform offers a capability called [Binary Authorization](https://cloud.google.com/binary-authorization/). Binary Authorization is a GCP managed service that works closely with GKE to enforce deploy-time security controls to ensure that only trusted container images are deployed. With Binary Authorization you can whitelist container registries, require images to be signed by trusted authorities, and centrally enforce those policies. By enforcing this policy, you can gain tighter control over your container environment by ensuring only approved and/or verified images are integrated into the build-and-release process.

This lab deploys a [Kubernetes Engine](https://cloud.google.com/kubernetes-engine/) Cluster with the Binary Authorization feature enabled demonstrates how to whitelist approved container registries, and walks you through the process of creating and running a signed container.

This lab was created by GKE Helmsman engineers to give you a better understanding of GKE Binary Authorization. You can view this demo on Github [here](https://github.com/GoogleCloudPlatform/gke-binary-auth-demo.git). We encourage any one to contribute to our assets!

## Architecture

The Binary Authorization and Container Analysis APIs are based upon the open-source projects Grafeas and Kritis.

*   Grafeas defines an API spec for managing metadata about software resources, such as container images, Virtual Machine (VM) images, JAR files, and scripts. You can use Grafeas to define and aggregate information about your project’s components.
*   Kritis defines an API for ensuring a deployment is prevented unless the artifact (container image) is conformant to central policy and optionally has the necessary attestations present.

In a simplified container deployment pipeline such as this:

![SDLC](https://cdn.qwiklabs.com/%2F23XvJfZJKvYRYkgWMwl8JHpZKz6d52x4%2B%2Fi%2BxZ3cOE%3D)

The container goes through at least 4 steps:

1.  The source code for creating the container is stored in source control.
2.  Upon committing a change to source control, the container is built and tested.
3.  If the build and test steps are completed, the container image artifact is then placed in a central container registry, ready for deployment.
4.  When a deployment of that container version is submitted to the Kubernetes API, the container runtime will pull that container image from the container registry and run it as a pod.

In a container build pipeline, there are opportunities to inject additional processes to signify or "attest" that each step was completed successfully. Examples include running unit tests, source control analysis checks, licensing verification, vulnerability analysis, and more. Each step could be given the power or "attestation authority" to sign for that step being completed. An "attestation authority" is a human or system with the correct PGP key and the ability to register that "attestation" with the Container Analysis API.

By using separate PGP keys for each step, each attestation step could be performed by different humans, systems, or build steps in the pipeline (a). Each PGP key is associated with an "attestation note" which is stored in the Container Analysis API. When a build step "signs" an image, a snippet of JSON metadata about that image is signed via PGP and that signed snippet is submitted to the API as a "note occurrence".

![SDLC](https://cdn.qwiklabs.com/RXPfaDeQ1Mv%2Bs2RYXmRFIDD%2Be1AoKAf9qEOD9m5t%2F80%3D)

(b).Once the container image has been built and the necessary attestations have been stored centrally, they are available for being queried as a part of a policy decision process. In this case, a [Kubernetes Admission Controller](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/), upon receiving an API request to `create` or `update` a Pod:

1.  Send a WebHook to the Binary Authorization API for a policy decision.
2.  The Binary Authorization policy is then consulted.
3.  If necessary, the Container Analysis API is also queried for the necessary attestation occurrences.
4.  If the container image conforms to the policy, it is allowed to run.
5.  If the container image fails to meet the policy, an error is presented to the API client with a message describing why it was prevented.

![SDLC](https://cdn.qwiklabs.com/2eczz6ago2COUKvuw9adq64v0g1SFqVaRTar7mLyTSc%3D)

## Setup

#### Before you click the Start Lab button

Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click **Start Lab**, shows how long Google Cloud resources will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

#### What you need

To complete this lab, you need:

*   Access to a standard internet browser (Chrome browser recommended).
*   Time to complete the lab.

**Note:** If you already have your own personal Google Cloud account or project, do not use it for this lab.

**Note:** If you are using a Pixelbook, open an Incognito window to run this lab.

#### How to start your lab and sign in to the Google Cloud Console

1.  Click the **Start Lab** button. If you need to pay for the lab, a pop-up opens for you to select your payment method. On the left is a panel populated with the temporary credentials that you must use for this lab.

    ![Open Google Console](https://cdn.qwiklabs.com/%2FtHp4GI5VSDyTtdqi3qDFtevuY014F88%2BFow%2FadnRgE%3D)

2.  Copy the username, and then click **Open Google Console**. The lab spins up resources, and then opens another tab that shows the **Sign in** page.

    ![Sign in](https://cdn.qwiklabs.com/VkUIAFY2xX3zoHgmWqYKccRLwFrR4BfARLd5ojmlbhs%3D)

    **_Tip:_** Open the tabs in separate windows, side-by-side.

    If you see the **Choose an account** page, click **Use Another Account**. ![Choose an account](https://cdn.qwiklabs.com/eQ6xPnPn13GjiJP3RWlHWwiMjhooHxTNvzfg1AL2WPw%3D)

3.  In the **Sign in** page, paste the username that you copied from the Connection Details panel. Then copy and paste the password.

    **_Important:_** You must use the credentials from the Connection Details panel. Do not use your Qwiklabs credentials. If you have your own Google Cloud account, do not use it for this lab (avoids incurring charges).

4.  Click through the subsequent pages:

    *   Accept the terms and conditions.
    *   Do not add recovery options or two-factor authentication (because this is a temporary account).
    *   Do not sign up for free trials.

After a few moments, the Cloud Console opens in this tab.

**Note:** You can view the menu with a list of Google Cloud Products and Services by clicking the **Navigation menu** at the top-left. ![Cloud Console Menu](https://cdn.qwiklabs.com/9vT7xPlxoNP%2FPsK0J8j0ZPFB4HnnpaIJVCDByaBrSHg%3D)

### Activate Cloud Shell

Cloud Shell is a virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud. Cloud Shell provides command-line access to your Google Cloud resources.

In the Cloud Console, in the top right toolbar, click the **Activate Cloud Shell** button.

![Cloud Shell icon](https://cdn.qwiklabs.com/vdY5e%2Fan9ZGXw5a%2FZMb1agpXhRGozsOadHURcR8thAQ%3D)

Click **Continue**.

![cloudshell_continue.png](https://cdn.qwiklabs.com/lr3PBRjWIrJ%2BMQnE8kCkOnRQQVgJnWSg4UWk16f0s%2FA%3D)

It takes a few moments to provision and connect to the environment. When you are connected, you are already authenticated, and the project is set to your _PROJECT_ID_. For example:

![Cloud Shell Terminal](https://cdn.qwiklabs.com/hmMK0W41Txk%2B20bQyuDP9g60vCdBajIS%2B52iI2f4bYk%3D)

`gcloud` is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

You can list the active account name with this command:

    gcloud auth list

(Output)

    Credentialed accounts:
     - <myaccount>@<mydomain>.com (active)

(Example output)

    Credentialed accounts:
     - google1623327_student@qwiklabs.net

You can list the project ID with this command:

    gcloud config list project

(Output)

    [core]
    project = <project_ID>

(Example output)

    [core]
    project = qwiklabs-gcp-44776a13dea667a6

For full documentation of `gcloud` see the [gcloud command-line tool overview](https://cloud.google.com/sdk/gcloud).

## Clone Demo

Clone the resources needed for this lab by running:

    git clone https://github.com/GoogleCloudPlatform/gke-binary-auth-demo.git

Go into the directory for this demo:

    cd gke-binary-auth-demo

### Set your region and zone

Certain Compute Engine resources live in regions and zones. A region is a specific geographical location where you can run your resources. Each region has one or more zones.

Learn more about regions and zones and see a complete list in [Regions & Zones documentation](https://cloud.google.com/compute/docs/regions-zones/).

Run the following to set a region and zone for your lab (you can use the region/zone that's best for you):

    gcloud config set compute/region us-central1
    gcloud config set compute/zone us-central1-a

## Set Default Cluster Version

Change the `create.sh`'s **GKE_VERSION** variable to `defaultClusterVersion`:

    sed -i 's/validMasterVersions\[0\]/defaultClusterVersion/g' ./create.sh

<ql-infobox>The default cluster version will be compatible with other dependencies in this lab.</ql-infobox>

## Deployment Steps

<ql-infobox>**NOTE:** The following instructions are applicable for deployments performed both with and without Cloud Shell.</ql-infobox>

To deploy the cluster, execute the following command. Feel free to replace the text `my-cluster-1` the name of the cluster that you would like to create.

    ./create.sh -c my-cluster-1

The `create` script will output the following message when complete:

    kubeconfig entry generated for my-cluster-1.
    NAME          LOCATION       MASTER_VERSION  MASTER_IP        MACHINE_TYPE   NODE_VERSION   NUM_NODES  STATUS
    my-cluster-1  us-central1-a  1.14.8-gke.17   104.154.181.211  n1-standard-1  1.14.8-gke.17  2          RUNNING
    Fetching cluster endpoint and auth data.
    kubeconfig entry generated for my-cluster-1.

The script will:

1.  Enable the necessary APIs in your project. Specifically, `container`, `containerregistry`, `containeranalysis`, and `binaryauthorization`.
2.  Create a new Kubernetes Engine cluster in your default ZONE, VPC and network.
3.  Retrieve your cluster credentials to enable `kubectl` usage.

It is safe to ignore warnings.

## Validation

The following script will validate that the demo is deployed correctly:

    ./validate.sh -c my-cluster-1

If the script fails it will output:

    Validation Failed: the BinAuthZ policy was NOT available

And / Or

    Validation Failed: the Container Analysis API was NOT available

If the script passes if will output:

    Validation Passed: the BinAuthZ policy was available
    Validation Passed: the Container Analysis API was available

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created a Kubernetes cluster with Binary Authorization, you will see an assessment score.

<ql-activity-tracking step="1">Create a Kubernetes Cluster with Binary Authorization</ql-activity-tracking>

### Using Binary Authorization

#### Managing the Binary Authorization Policy

To access the Binary Authorization Policy configuration UI, perform the following steps:

*   In the GCP console navigate to the **Security** > **Binary Authorization**.
*   Click **Configure Policy**.

![Security_binauth_console.png](https://cdn.qwiklabs.com/iLGX43IaKasHLhOGaaO%2FK1PJShS4kMfjjL%2BM5i1UWvY%3D)

<ql-infobox>To access the Binary Authorization Policy configuration via `gcloud`:

*   Run `gcloud beta container binauthz policy export > policy.yaml`
*   Make the necessary edits to `policy.yaml`
*   Run `gcloud beta container binauthz policy import policy.yaml`

</ql-infobox>

The policy you are editing is the "default" policy, and it applies to _all GKE clusters in the GCP project_ unless a cluster-specific policy is in place. The recommendation is to create policies specific to each cluster and achieve successful operation (whitelisting registries as needed), and then set the default project-level policy to "Deny All Images". Any new cluster in this project will then need its own cluster-specific policy.

After clicking **Configure Policy**, the following will appear. Click the down arrow next to Image paths to display them:

![edit_policy.png](https://cdn.qwiklabs.com/QzCd%2FZYJPgJrVpwPHB%2BkXzXK1Xeq%2BwrZgn9IuKRiou4%3D)

The default policy rule is to `Allow all images`. This mimics the behavior as if Binary Authorization wasn't enabled on the cluster. If the default rule is changed to `Disallow all images` or `Allow only images that have been approved by all of the following attestors`, then images that do not match the exempted registry image paths or do not have the required attestations will be blocked, respectively.

Next, you will make some edits to the policy:

*   Change your project default rule to `Disallow all images`
*   Click down arrow in the Rules section, then click **Add Rule**.
*   In the **Add cluster-specific rule** field, enter your location and cluster name in the form `location.clustername`. e.g. `us-central1-a.my-cluster-1` which corresponds to the zone `us-central1-a` and the cluster name `my-cluster-1`.
*   Select the default rule of `Allow all images` for your cluster.
*   Click **Add**.

![Security_cluster_rule.png](https://cdn.qwiklabs.com/3zqOrAae3X4qVwJgDg%2FMFQxorPqBpqZoxVAKtLvZfDc%3D)

Click **Save Policy**.

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully updated Binary Authorization Policy to add Disallow all images rule at project level and allow all images at cluster level, you will see an assessment score.

<ql-activity-tracking step="2">Update Binary Authorization Policy to add Disallow all images rule at project level and allow at cluster level</ql-activity-tracking>

## Creating a Private GCR Image

To simulate a real-world configuration, create a private GCR container image in your project. You will pull down the `nginx` container from `gcr.io/google-containers/nginx` project and push it to your own GCR repository without modification.

In Cloud Shell, pull down the `latest` `nginx` container:

    docker pull gcr.io/google-containers/nginx:latest

Authenticate docker to the project:

    gcloud auth configure-docker

Set the PROJECT_ID shell variable:

    PROJECT_ID="$(gcloud config get-value project)"

Tag and push it to the current project's GCR:

    docker tag gcr.io/google-containers/nginx "gcr.io/${PROJECT_ID}/nginx:latest"
    docker push "gcr.io/${PROJECT_ID}/nginx:latest"

List the "private" nginx image in your own GCR repository:

    gcloud container images list-tags "gcr.io/${PROJECT_ID}/nginx"

#### Denying All Images

To prove that image denial by policy will eventually work as intended, first verify that the cluster-specific `allow` rule is in place and allows all containers to run.

To do this, launch a single `nginx` pod:

    cat << EOF | kubectl create -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: "gcr.io/${PROJECT_ID}/nginx:latest"
        ports:
        - containerPort: 80
    EOF

You should see a message stating that `pod/nginx created`.

List the pods:

    kubectl get pods

(Output)

    NAME    READY     STATUS    RESTARTS   AGE
    nginx   1/1       Running   0          1m

If this fails recheck your cluster-specific region and name and try again.

Now, delete this pod:

    kubectl delete pod nginx

Next, prove that the Binary Authorization policy can block undesired images from running in the cluster.

On the Binary Authorization page, click **Edit Policy**,

Expand the **Rules** dropdown, click on the three vertical dots to the right of your Cluster rule, and click **edit**. Then, select `Disallow all images`, click **Submit**.

Your policy should look similar to the following:

![Binary Authorization Block All Policy](https://cdn.qwiklabs.com/qloqkV2nLQMIxqlfnwK01UDp0HnqlcYiCXAvUnlV%2FvU%3D)

Finally, click **Save Policy** to apply those changes.

<ql-infobox>**Important:** Wait at least 30 seconds before proceeding to allow the policy to take effect.</ql-infobox>

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully updated Binary Authorization Policy to Disallow all images rule at cluster level, you will see an assessment score.

<ql-activity-tracking step="3">Update cluster specific policy to Disallow all images</ql-activity-tracking>

Now, run the same command as before to create the static `nginx` pod:

    cat << EOF | kubectl create -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: "gcr.io/${PROJECT_ID}/nginx:latest"
        ports:
        - containerPort: 80
    EOF

This time, though, you should receive a message from the API server indicating that the policy prevented this pod from being successfully run:

    Error from server (Forbidden): error when creating "STDIN": pods "nginx" is forbidden: image policy webhook backend denied one or more images: Denied by cluster admission rule for us-east4-a.my-cluster-1\. Overridden by evaluation mode

To be able to see when any and all images are blocked by the Binary Authorization Policy, navigate to the GKE Audit Logs in Stackdriver and filter on those error messages related to this activity.

1.  In the GCP console navigate to the **Navigation menu** > **Logging**.

2.  On this page, click the down arrow on the far right of the "Filter by label or text search" input field, and select **Convert to advanced filter**.

3.  Populate the text box with:

    resource.type="k8s_cluster" protoPayload.response.reason="Forbidden"

1.  Click **Submit filter**.

2.  You should see errors corresponding to the blocking of the `nginx` pod from running.

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully verified cluster admission rule, you will see an assessment score.

<ql-activity-tracking step="4">Create a Nginx pod to verify cluster admission rule is applied for disallow all images (denies to create)</ql-activity-tracking>

#### Denying Images Except From Whitelisted Container Registries

Let's say that you actually want to allow _just_ that nginx container to run. The quickest step to enable this is to _whitelist_ the registry that it comes from.

You will use the output of the following command as your image path:

    echo "gcr.io/${PROJECT_ID}/nginx*"

Copy the image path output to your buffer.

Edit the Binary Authorization Policy, display the image paths, then click **Add Image Path**.

Paste in the image path you copied earlier and click **Done**. The image below shows an example path.

![Binary Authorization Whitelist](https://cdn.qwiklabs.com/Nudcbk0vyw7lM0ATGeyrRytmmlLgML9kft2T2E20jkA%3D)

**Save Policy**, then run:

    cat << EOF | kubectl create -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: "gcr.io/${PROJECT_ID}/nginx:latest"
        ports:
        - containerPort: 80
    EOF

You should now be able to launch this pod and prove that registry whitelisting is working correctly.

Run the following to clean up and prepare for the next steps:

    kubectl delete pod nginx

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully updated Binary Authorization policy to whitelist the container registry, you will see an assessment score.

<ql-activity-tracking step="5">Update BA policy to denying images except from whitelisted container registries (your project container registry)</ql-activity-tracking>

#### Enforcing Attestations

Whitelisting container image registries is a great first step in preventing undesired container images from being run inside a cluster, but there is more you can do to ensure the container was built correctly. You want to cryptographically verify that a given container image was approved for deployment. This is done by an "attestation authority" which states or _attests_ to the fact that a certain step was completed. The attestation authority does this by using a PGP key to _sign_ a snippet of metadata describing the SHA256 hash of a container image and submitting it to a central metadata repository--the Container Analysis API.

Later, when the Admission Controller goes to validate if a container image is allowed to run by consulting a Binary Authorization policy that requires attestations to be present on an image, it will check to see if the Container Analysis API holds the signed snippet(s) of metadata saying which steps were completed. With that information, the Admission Controller will know whether to allow or deny that pod from running.

Next, perform a manual attestation of a container image. You will take on the role of a human attestation authority and will perform all the steps to sign a container image, create a policy to require that attestation to be present on images running inside your cluster, and then successfully run that image in a pod.

#### Setting up the Necessary Variables

Attestor name/email details:

    ATTESTOR="manually-verified" # No spaces allowed
    ATTESTOR_NAME="Manual Attestor"
    ATTESTOR_EMAIL="$(gcloud config get-value core/account)" # This uses your current user/email

Container Analysis Note ID/description of your attestation authority:

    NOTE_ID="Human-Attestor-Note" # No spaces
    NOTE_DESC="Human Attestation Note Demo"

Names for files to create payloads/requests:

    NOTE_PAYLOAD_PATH="note_payload.json"
    IAM_REQUEST_JSON="iam_request.json"

#### Creating an Attestation Note

The first step is to register the attestation authority as a [Container Analysis note](https://cloud.google.com/terms/launch-stages) with the Container Analysis API. To do this, you'll create an `ATTESTATION` note and submit it to the API.

Create the `ATTESTATION` note payload:

    cat > ${NOTE_PAYLOAD_PATH} << EOF
    {
      "name": "projects/${PROJECT_ID}/notes/${NOTE_ID}",
      "attestation_authority": {
        "hint": {
          "human_readable_name": "${NOTE_DESC}"
        }
      }
    }
    EOF

Submit the `ATTESTATION` note to the Container Analysis API:

    curl -X POST \
        -H "Content-Type: application/json" \
        -H "Authorization: Bearer $(gcloud auth print-access-token)"  \
        --data-binary @${NOTE_PAYLOAD_PATH}  \
        "https://containeranalysis.googleapis.com/v1beta1/projects/${PROJECT_ID}/notes/?noteId=${NOTE_ID}"

You should see the output from the prior command display the created note, but the following command will also list the created note:

    curl -H "Authorization: Bearer $(gcloud auth print-access-token)"  \
        "https://containeranalysis.googleapis.com/v1beta1/projects/${PROJECT_ID}/notes/${NOTE_ID}"

#### Creating a PGP Signing Key

As your attestation authority uses a PGP key to perform the cryptographic signing of the image metadata, create a new PGP key and export the public PGP key.

<ql-warningbox>**Note:** This error messages related to this activity. PGP key is not password protected for this exercise. In a production system, be sure to properly safeguard your private PGP keys.</ql-warningbox>

Set another shell variable:

    PGP_PUB_KEY="generated-key.pgp"

Create the PGP key:

    sudo apt-get install rng-tools

    sudo rngd -r /dev/urandom

    gpg --quick-generate-key --yes ${ATTESTOR_EMAIL}

(Press **Enter** to use an empty passphrase and acknowledge warnings).

Extract the public PGP key:

    gpg --armor --export "${ATTESTOR_EMAIL}" > ${PGP_PUB_KEY}

#### Registering the Attestor in the Binary Authorization API

The next step is to create the "attestor" in the Binary Authorization API and add the public PGP key to it.

Create the Attestor in the Binary Authorization API:

    gcloud --project="${PROJECT_ID}" \
        beta container binauthz attestors create "${ATTESTOR}" \
        --attestation-authority-note="${NOTE_ID}" \
        --attestation-authority-note-project="${PROJECT_ID}"

Add the PGP Key to the Attestor:

    gcloud --project="${PROJECT_ID}" \
        beta container binauthz attestors public-keys add \
        --attestor="${ATTESTOR}" \
        --pgp-public-key-file="${PGP_PUB_KEY}"

List the newly created Attestor:

    gcloud --project="${PROJECT_ID}" \
        beta container binauthz attestors list

The output should look similar to the following:

    ┌───────────────────┬────────────────────────────────────────────────────────────┬─────────────────┐
    │        NAME       │                            NOTE                            │ NUM_PUBLIC_KEYS │
    ├───────────────────┼────────────────────────────────────────────────────────────┼─────────────────┤
    │ manually-verified │ projects/<myprojectnamehere>/notes/Human-Attestor-Note     │ 1               │
    └───────────────────┴────────────────────────────────────────────────────────────┴─────────────────┘

#### "Signing" a Container Image

The preceeding steps only need to be performed once. From this point on, this step is the only step that needs repeating for every new container image.

The `nginx` image at `gcr.io/google-containers/nginx:latest` is already built and available for use. Perform the manual attestations on it as if it were your own image built by your own processes and save the step of having to build it.

Set a few shell variables:

    GENERATED_PAYLOAD="generated_payload.json"
    GENERATED_SIGNATURE="generated_signature.pgp"

Get the PGP fingerprint:

    PGP_FINGERPRINT="$(gpg --list-keys ${ATTESTOR_EMAIL} | head -2 | tail -1 | awk '{print $1}')"

Obtain the SHA256 Digest of the container image:

    IMAGE_PATH="gcr.io/${PROJECT_ID}/nginx"
    IMAGE_DIGEST="$(gcloud container images list-tags --format='get(digest)' $IMAGE_PATH | head -1)"

Create a JSON-formatted signature payload:

    gcloud beta container binauthz create-signature-payload \
        --artifact-url="${IMAGE_PATH}@${IMAGE_DIGEST}" > ${GENERATED_PAYLOAD}

View the generated signature payload:

    cat "${GENERATED_PAYLOAD}"

"Sign" the payload with the PGP key:

    gpg --local-user "${ATTESTOR_EMAIL}" \
        --armor \
        --output ${GENERATED_SIGNATURE} \
        --sign ${GENERATED_PAYLOAD}

View the generated signature (PGP message):

    cat "${GENERATED_SIGNATURE}"

Create the attestation:

    gcloud beta container binauthz attestations create \
        --artifact-url="${IMAGE_PATH}@${IMAGE_DIGEST}" \
        --attestor="projects/${PROJECT_ID}/attestors/${ATTESTOR}" \
        --signature-file=${GENERATED_SIGNATURE} \
        --public-key-id="${PGP_FINGERPRINT}"

View the newly created attestation:

    gcloud beta container binauthz attestations list \
        --attestor="projects/${PROJECT_ID}/attestors/${ATTESTOR}"

#### Running an Image with Attestation Enforcement Enabled

The next step is to change the Binary Authorization policy to enforce that attestation is to be present on all images that do not match the whitelist pattern(s).

To change the policy to require attestation, run the following and then copy the full path/name of the attestation authority:

    echo "projects/${PROJECT_ID}/attestors/${ATTESTOR}" # Copy this output to your copy/paste buffer

Next, edit the Binary Authorization policy to `edit` the cluster-specific rule.

Select `Allow only images that have been approved by all of the following attestors` instead of `Disallow all images` in the pop-up window.:

Click the three dots by your cluster name to **Edit** your cluster rules.

![Edit Policy](https://cdn.qwiklabs.com/CVoPKpCQJMFdBQm3Q9OZxcfA9XuX2A7v6CzXGStsHO4%3D)

Next, click on `Add Attestors` followed by `Add attestor by resource ID`. Enter the contents of your copy/paste buffer in the format of `projects/${PROJECT_ID}/attestors/${ATTESTOR}`, then click `Add 1 Attestor`, `Submit`, and finally `Save Policy`.

![Edit Policy](https://cdn.qwiklabs.com/wESAAXO%2BluFwDX%2FhJmhITo9I5t%2Bj1BGlnTGTGIT3PS4%3D)

The default policy should still show `Disallow all images`, but the cluster-specific rule should be requiring attestation.

Now, obtain the most recent SHA256 Digest of the signed image from the previous steps:

    IMAGE_PATH="gcr.io/${PROJECT_ID}/nginx"
    IMAGE_DIGEST="$(gcloud container images list-tags --format='get(digest)' $IMAGE_PATH | head -1)"

After waiting at least 30 seconds from the time the Binary Authorization policy was updated, run the pod and verify success:

    cat << EOF | kubectl create -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: "${IMAGE_PATH}@${IMAGE_DIGEST}"
        ports:
        - containerPort: 80
    EOF

Congratulations! You have now manually attested to a container image and enforced a policy for that image inside your GKE cluster.

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully updated Binary Authorization policy to modify cluster specific rule to allow only images that have been approved by attestors, you will see an assessment score.

<ql-activity-tracking step="6">Update BA policy to modify cluster specific rule to allow only images that have been approved by attestors</ql-activity-tracking>

#### Handling Emergency Situations

From a user's perspective, the Binary Authorization policy may incorrectly block an image or there may be another issue with the successful operation of the admission controller webhook. In this "emergency" case, there is a "break glass" capability that leverages a specific annotation to signal to the admission controller to run the pod and skip policy enforcement.

Note: You will want to notify a security team when this occurs as this can be leveraged by malicious users if they have the ability to create a pod. In this case, though, your response procedures can be started within seconds of the activity occurring. The logs are available in Stackdriver:

1.  In the GCP console navigate to the **Operations** > **Logging** page.

2.  Click the downward arrow on the far right of the "Filter by label or text search" input field, and select **Convert to advanced filter**. Populate the text box with:

        resource.type="k8s_cluster" protoPayload.request.metadata.annotations."alpha.image-policy.k8s.io/break-glass"="true"

3.  You should see events when the admission controller allowed a pod due to the annotation being present. From this filter, you can create a `Sink` which sends logs that match this filter to an external destination.

![Stackdriver break-glass filter](https://cdn.qwiklabs.com/h5EIZT2E%2F5rPKyKTkWcsQEmPr50Hdot1WbxMnHqOYJg%3D)

1.  To run an unsigned `nginx` container with the "break glass" annotation, run:

    cat << EOF | kubectl create -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx-alpha
      annotations:
        alpha.image-policy.k8s.io/break-glass: "true"
    spec:
      containers:
      - name: nginx
        image: "nginx:latest"
        ports:
        - containerPort: 80
    EOF

## Tear Down

Qwiklabs will remove all resources you created for this lab, but it's good to know how to clean up your own environment.

The following script will destroy the Kubernetes Engine cluster:

    ./delete.sh -c my-cluster-1

If you created your own cluster name at the beginning of the lab, use that name. In this example the name `my-cluster-1` was used.

The last lines of the output will be:

    Deleting cluster

<ql-infobox>**Note:** Cluster delete command is being run asynchronously and will take a few moments to be removed. Use the **Cloud Console UI** or `gcloud container clusters list` command to track the progress if desired. Wait until cluster get removed.</ql-infobox>

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully deleted your cluster, you will see an assessment score.

<ql-activity-tracking step="7">Tear Down (delete cluster)</ql-activity-tracking>

The following commands will remove the remaining resources.

Delete the container image that was pushed to GCR:

    gcloud container images delete "${IMAGE_PATH}@${IMAGE_DIGEST}" --force-delete-tags

Delete the Attestor:

    gcloud --project="${PROJECT_ID}" \
        beta container binauthz attestors delete "${ATTESTOR}"

Delete the Container Analysis note:

    curl -X DELETE \
        -H "Authorization: Bearer $(gcloud auth print-access-token)" \
        "https://containeranalysis.googleapis.com/v1beta1/projects/${PROJECT_ID}/notes/${NOTE_ID}"

## Troubleshooting in your own environment

1.  If you update the Binary Authorization policy and very quickly attempt to launch a new pod/container, the policy might not have time to take effect. You may need to wait 30 seconds or more for the policy change to become active. To retry, delete your pod using `kubectl delete <podname>` and resubmit the pod creation command.

2.  Run `gcloud container clusters list` command to check the cluster status.

3.  If you enable additional features like `--enable-network-policy`, `--accelerator`, `--enable-tpu`, or `--enable-metadata-concealment`, you may need to add additional registries to your Binary Authorization policy whitelist for those pods to be able to run. Use `kubectl describe pod <podname>` to find the registry path from the image specification and add it to the whitelist in the form of `gcr.io/example-registry/*` and save the policy.

4.  If you get errors about quotas, please increase your quota in the project. See [here](https://cloud.google.com/compute/quotas) for more details.

## Relevant Materials

1.  [Google Cloud Quotas](https://cloud.google.com/compute/quotas)
2.  [Signup for Google Cloud](https://cloud.google.com)
3.  [Google Cloud Shell](https://cloud.google.com/shell/docs/)
4.  [Binary Authorization in GKE](https://cloud.google.com/binary-authorization/)
5.  [Container Analysis notes](https://cloud.google.com/binary-authorization/docs/key-concepts#analysis_notes)
6.  [Kubernetes Admission Controller](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/)
7.  [Launch Stages](https://cloud.google.com/terms/launch-stages)

## Congratulations

![gke_best_security_badge.png](https://cdn.qwiklabs.com/maNNnEGq9cLqRzxQkol4VrPlDzyruxMCXVMlMRHwRhM%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs [Google Kubernetes Engine Best Practices: Security](https://google.qwiklabs.com/quests/64) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/quests/64/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take your next lab

Continue your Quest with [How to Use a Network Policy in Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/1764), or check out these suggestions:

*   [Using Role-based Access Control in Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/1720)
*   [Hardening Default GKE Cluster Configurations](https://google.qwiklabs.com/catalog_lab/1722)
