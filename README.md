
## Data source

MIT Supercloud Datacenter Challenge dataset (CC BY-NC-ND 4.0), public AWS S3 bucket, no account needed.

- Prefix: `s3://mit-supercloud-dataset/datacenter-challenge/202201/`
- List: `aws s3 ls --no-sign-request s3://mit-supercloud-dataset/datacenter-challenge/202201/`
- Used: `gpu/` (one CSV per job, 100 ms), `slurm-log.csv` (~103 MB), `labelled_jobids.csv`
- Not used: `cpu/`, `node-data.csv` (~2.27 GB)
- Raw and processed data are never committed (license: NoDerivatives); only scripts are published.
