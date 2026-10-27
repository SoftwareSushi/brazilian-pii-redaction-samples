# Brazilian Personal Data Redaction: samples and notebook

Sample inputs, outputs and a SageMaker notebook for **Brazilian Personal Data Redaction for Portuguese Text (LGPD)**, a model package on AWS Marketplace from Software Sushi.

The model finds and removes Brazilian personal data from Portuguese text inside your own AWS account, with no network access: CPF, CNPJ (including the alphanumeric format), RG, CNH, PIS/PASEP, voter ID, OAB, RENAVAM, vehicle plates, bank branch and account, card numbers, phones, emails, CEP, addresses, PIX keys, dates of birth and people's names.

## Run the notebook

1. Subscribe to the product in AWS Marketplace.
2. In SageMaker, open a notebook instance or a Studio space whose role can run endpoints and batch transform jobs (`AmazonSageMakerFullAccess` is enough).
3. Upload [`brazilian-pii-redaction.ipynb`](brazilian-pii-redaction.ipynb) and run it from the top.

It creates a real-time endpoint on `ml.m5.large`, tries the options, runs three batch transform jobs (JSON Lines, CSV and plain text) and deletes everything at the end. It takes about 20 minutes and under $0.10 of AWS charges. All sample data is written into the notebook.

## Samples

Every value in the samples is fake. The outputs were produced with the key `exemplo` (`BRPII_KEY=exemplo`); with your own key the placeholders differ.

| Use | Input | Output |
|---|---|---|
| Real-time, `application/json` | [input-realtime.json](samples/input-realtime.json) | [output-realtime.json](samples/output-realtime.json) |
| Batch, `application/jsonlines` | [input-batch.jsonl](samples/input-batch.jsonl) | [output-batch.jsonl](samples/output-batch.jsonl) |
| Batch, `text/csv` (all fields cleaned) | [input-batch.csv](samples/input-batch.csv) | [output-batch.csv](samples/output-batch.csv) |
| Batch, `text/plain` | [input-batch.txt](samples/input-batch.txt) | [output-batch.txt](samples/output-batch.txt) |

## Input and output in brief

**Real-time.** Send `{"text": "..."}` or `{"texts": [...]}` as `application/json`, with optional `"options"`. The response is `{"results": [{"text": cleaned text, "findings": [...]}]}`, each finding with its type, its position in the original text, the action taken and the placeholder. `text/plain` and `text/csv` are accepted too.

**Batch transform.** JSON Lines (`{"id": ..., "text": ...}` per line), CSV or plain-text files in S3, with `SplitType` `Line` and `Accept` the same as `ContentType`. Each output file has the same shape as its input.

**Options.** `action` (`replace`, the default; `mask`, `remove`, `report`), `per_label` (an action per type), `labels` (only these types), `include_values` (also return the original values; off by default), and for CSV `csv_columns` (column numbers from 0) and `csv_findings`. For batch jobs, put options in the `BRPII_OPTIONS` environment variable as JSON.

**Placeholder key.** Set `BRPII_KEY` on the model, or send `key=...` in the `X-Amzn-SageMaker-Custom-Attributes` header. With the same key, the same value always gets the same placeholder, so cleaned records can still be joined. Keep the key secret: this is pseudonymisation, not anonymisation.

**Limits.** A real-time request has 60 seconds; keep each to about 50 short texts or 20,000 characters. Portuguese text only.

## Support

Email info@softwaresushi.com on business days; we reply within one or two business days. Software Sushi builds custom machine learning and AI systems: https://softwaresushi.com.

## Licence

The notebook and samples in this repository are under the [MIT No Attribution licence](LICENSE). The model itself is licensed through AWS Marketplace.
