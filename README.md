# costctl - G7 W6 side challenge

`costctl` is a mini Python CLI for listing, tagging, terminating, and cleaning
AWS resources used in the XBrain W6 cost-control side challenge.

## Result

- Group: G7
- Tests: 25/25 passing
- Implemented: `list`, `terminate`, `tag`, `clean`
- Demo tag: `Application=HealthBot`
- Demo bucket: `hello-bucket-311912733822`

## Setup

```bash
pip install -r requirements-dev.txt
```

Configure AWS credentials before running against a real account:

```bash
aws configure
```

## Test

```bash
pytest -v tests/
```

Expected result:

```text
25 passed
```

(img\test.png)

## Usage

List resources:

```bash
python ./costctl.py list ec2
python ./costctl.py list ec2 --missing-tag Application
python ./costctl.py list volume
python ./costctl.py list s3 --tag Application=HealthBot
python ./costctl.py list s3 --missing-tag Application
```

Apply tags:

```bash
python ./costctl.py tag s3 --id hello-bucket-311912733822 --set Application=HealthBot --set Environment=dev --set Owner=Nghia --set purpose=practice
```

Terminate one resource:

```bash
python ./costctl.py terminate s3 --id hello-bucket-311912733822
```

Bulk clean resources by tag. This is a dry run unless `--apply` is provided:

```bash
python ./costctl.py clean --tag purpose=practice
python ./costctl.py clean --tag purpose=practice --apply
```

## Real AWS Verification

The following commands were run against the workshop AWS account.

```text
python ./costctl.py list ec2
EC2 all — 0 found:
------------------------------------------------------------------------------

python ./costctl.py list ec2 --missing-tag Application
EC2 missing:Application — 0 found:
------------------------------------------------------------------------------

python ./costctl.py list volume
VOLUME all — 0 found:
------------------------------------------------------------------------------

python ./costctl.py list s3 --tag Application=HealthBot
S3 Application=HealthBot — 1 found:
------------------------------------------------------------------------------
  hello-bucket-311912733822 bucket         active       Application=HealthBot, Environment=dev, Owner=Nghia, purpose=practice

python ./costctl.py list s3 --missing-tag Application
S3 missing:Application — 0 found:
------------------------------------------------------------------------------
```

Sample outputs are saved in `sample_output/`.

(img\sample.png)

## Project Structure

```text
costctl.py
commands/
  _common.py
  list_cmd.py
  terminate_cmd.py
  tag_cmd.py
  clean_cmd.py
tests/
sample_output/
```

## Notes

- EC2 and EBS volume outputs show `0 found` because the demo account did not
  have active EC2 instances or volumes at verification time.
- S3 was used as the real tagged demo resource because it is simple, low cost,
  and easy to delete after verification.
- `terminate` asks for confirmation by default unless `--force` is used.
- `clean` is dry-run by default and only deletes resources when `--apply` is
  passed.

## Team

- Nghia
