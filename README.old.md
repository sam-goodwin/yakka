
Packyak makes it easy to build Lakehouses, Data Pipelines and and AI applications on AWS.

# Roadmap

- [x] `StreamlitSite` - deploy a Streamlit application to ECS with VPC and Load Balancing
- [ ] Infer least privilege IAM Policies for Streamlit scripts (`home.py`, `pages/*.py`)
- [x] `@function` - host an Lambda Function
- [x] Infer least privilege IAM Policies for functions
- [x] `Bucket` - work with files in S3, attach event handlers
- [x] `Queues` - send messages to, attach event handlers
- [ ] `Stream` - send and consume records through AWS Kinesis
- [ ] `Table` - store structured data (Parquet, Orc, etc.) in a Glue Catalog. Model data using `pydantic`
- [ ] `@asset` - build data pipelines with dependency graphs
- [ ] `@train` - capture the inputs and outputs of a function for ML training and human feedback
- [ ] Generate audit reports for HIPAA and GDPR compliance policies

# Setup


## Pre-requisites

1. Install [Session Manager plugin for the AWS CLI](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html) for connecting to your AWS resources from your local machine.
2. Docker (for bundling Python applications for the target runtime, e.g. in an Amazon Linux Lambda Function)
3. Python Poetry

```sh
curl -sSL https://install.python-poetry.org | python3 -
```

4. `poetry-plugin-export` - see https://python-poetry.org/docs/plugins/#using-plugins

```sh
poetry self add poetry-plugin-export
```

# How To: Deploy Streamlit

## Custom Domain

1. Create a Hosted Zone
2. Transfer the DNS nameservers from your DNS provider to the Hosted Zone
3. Create a Certificate

## HTTPS

1. Create a Certificate via the AWS Console

# Example

> 🔧 Note: Packyak is in active development. Not all features are implemented. Check back to see the following example grow.

Below is the most simple Packyak application: a Bucket with a Function that writes to it.

Your application's infrastructure is declared in code. The Packyak compiler analyzes it to auto-provision cloud resources (in this case AWS S3 Bucket and Lambda Function) with least privilege IAM Policy inference.

```py
from packyak import Bucket, function

videos = Bucket("videos")

@function()
async def upload_video():
    await videos.put("key", "value")

@videos.on("create")
async def on_uploaded_video(event: Bucket.ObjectCreatedEvent):
  video = await videos.get(event.key)
  transcription

@asset()
async def transcribed_videos():
  ...
```

# Nessie CLI Setup

TODO: should be done as part of `packyak init`

```sh
pip install pynessie

mkdir -p ~/.config

cat <<EOF > ~/.config/nessie
auth:
    type: aws
    timeout: 10
endpoint: http://localhost:19120/api/v1
verify: yes
EOF
```

# Connect to EMR over SSH

See: [sagemaker-ssh-helper](https://github.com/aws-samples/sagemaker-ssh-helper)

```sh
sm-local-configure
```