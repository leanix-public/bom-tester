## SBOM-Tester
[![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)

This a simple script that takes a [CycloneDX SBOM](https://cyclonedx.org/) in JSON format and analyses it against certain quality factors. Namely,
1. Correctness and presence of [PURLs](https://github.com/package-url/purl-spec).
2. Correctness and presence of licenses per purl. Note: it correlates against [SPDX-license ids](https://github.com/spdx/license-list-data).
3. Whether the SBOM entails the [dependency tree](https://cyclonedx.org/docs/1.4/json/#dependencies)
4. The presence of an [operating system](https://cyclonedx.org/docs/1.4/json/#components_items_type)
5. Whether the SBOM contains information on the tool that was used to create it


### Usage

1. Install required libraries
```python
pip install -r requirements.txt
```

Note: create a virtual env first
```bash

python -m env venv

source env/bin/activate

```

2. load a JSON CycloneDX file in `main.py`

```python
with open("<your-sbom.json>", "r") as file:
        sbom = json.loads(file.read())

```


3. Run `main.py`
```bash
python main.py
```

4. Example output
```python
Results 

Found 301 purls.
301 / 301 (100.0%) purls are valid.
293 / 301 (97.0%) contain SPDX-compliant license ids.
SBOM is schema compliant.
SBOM does not contain OS information.
The SBOM generation tool was found: [{'vendor': 'CycloneDX', 'name': 'cyclonedx-gradle-plugin', 'version': '1.7.3'}].
The SBOM contains a dependency tree.
The overall SBOM quality score is: 0.895/1.

```

Note the `./test` folder contains CycloneDX SBOMs created for the [vsm-webshop project](https://github.com/leanix-public/vsm-webshop-demo) with the [CycloneDX python plugin](https://github.com/CycloneDX/cyclonedx-python), [Syft](https://github.com/anchore/syft), [Trivy](https://github.com/aquasecurity/trivy) and [ORT](https://github.com/oss-review-toolkit/ort). Feel free to play with those.

### Scoring 

You'll find that in `analyser.py` the function `grade_sbom` contains the weights for the above the factors:
```python
 weights = {
        "has_dependency_tree": 0.2,
        "valid_bom": 0.1,
        "has_operating_system": 0.1,
        "valid_licenses": 0.1,
        "valid_purls": 0.5

    }

```

and further down you'll also see the weighted scoring
```python
score = (score_dep_tree * weights['has_dependency_tree']) + (score_valid_bom * weights['valid_bom']) + (
        score_operating_systems * weights['has_operating_system']) + (score_licenses * weights['valid_licenses']) + (score_purls * weights['valid_purls'])
```

In simple words, the scoring looks at:
1. whether the SBOM has a [dependency tree](https://cyclonedx.org/docs/1.4/json/#dependencies)
2. the BOM file is of a [valid CycloneDX schema](https://cyclonedx.org/docs/1.4/json/)
3. Contains an [operating system](https://cyclonedx.org/docs/1.4/json/#components_items_type)
4. Has [SPDX-valid license ids](https://github.com/spdx/license-list-data)
5. Has [purls](https://github.com/package-url/purl-spec) that conform with the spec.

#### Changing the weights
Feel free to change the `weights` array according to your needs. Ensure that all weights add up to 1 ;) 

### Contact
Feel free to contact me for any queries under vincent.groves@leanix.net or just open a issue or PR. Will try to get back to you in a timely manner.