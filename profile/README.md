# Introduction

Welcome to the public organisation to store software developed by members of the IMS-MRC Epidemiology Unit for the
UK Biobank [Research Access Platform (RAP)](https://dnanexus.gitbook.io/uk-biobank-rap/). The information here describes
individual software and workflows stored as repositories within this organisation.

This organisation and many of the repositories here-in are maintained by [Eugene Gardner](https://github.com/eugenegardner):

eugene.gardner at mrc-epid.cam.ac.uk

Other maintainers include:

T.B.D.

### Table of Contents

- [Relevant DNA Nexus Projects](#relevant-dna-nexus-projects)
- [Using Applets on DNANexus](#using-applets-on-dnanexus)
    * [Cloning an Applet](#cloning-an-applet)
    * [Docker Images](#docker-images)
        + [Building A Docker Image](#building-a-docker-image)
- [Workflows](#workflows)
    * [VCF Filtering and Rare Variant Burden Testing](#vcf-filtering-and-rare-variant-burden-testing)
        + [Provided Resources](#provided-resources)
            - [Combined VEP Annotations](#combined-vep-annotations)
            - [Sample Lists](#sample-lists)
- [Tutorials](#tutorials)
- [Software](#software)

# Relevant DNA Nexus Projects

DNANexus stores data and resources within individual projects. Projects are whatever you choose for them to be, but in
the case of this organisation, we have siloed individual "ideas" within a project to ensure that data and resources are
kept separate. For example, we can have one project studying the effects of rare variation on testosterone levels and
another on a GWAS of age at voice breaking. These two "ideas" will be stored in separate projects on the DNANexus platform
and each will receive:

* A unique hash identifier like: project-1234567890ABCDEFGHIJ
* A separate set of linked UKBiobank resources (if requested) free of charge
* An independent billing profile (allowing for billing to different organisations/research groups)

This means we can essentially create as many projects as we would like to keep thoughts/ideas/people organised. The one
limitation to this is sharing of software/tools created in one project versus another. Please [see below](#using-applets-on-dnanexus)
what this means.

This organisation has three seperate projects:

| project name | brief description | project hash |
| ------------ | ----------------- | ------------ |
| MRC_EPID_450K_read_depth | Effects of rare variation on somatic loss of the Y-chromosome | project-G6F3238JvzZpKfB7FbbYpX92 |
| MRC - Variant Filtering 450k | Variant filtering and rare variation burden testing software | project-G6BJF50JJv8p4PjGB9yy7YQ2 |
| genetics_of_miscarriage_relaunch | Effects of rare homozygous LoF variation on miscarraige | project-G6Z05V8JZGX3JBB9JpBqX6V7 |

# Using Applets on DNANexus

When generating software/tools for the DNANexus platform, one can create either an *Applet* or an *App*. The differences
are documented in more detail [here](https://documentation.dnanexus.com/developer/apps/intro-to-building-apps), but in
brief:

* Applets are specific to a project
* Apps are visible to all projects on the DNANexus platform (with some settable restrictions by the developer).

Here, we have provided the sourcecode for applets, and thus each repository within this organisation is the source code
for an "applet". Please see the DNANexus documentation on applets to learn about how they are created, maintained, and run:

https://documentation.dnanexus.com/developer/apps.

Each applet in this organisation comes with three (among other) files:

1. Source code – Source code is located in `src/` as a single script with a `.py` suffix. Source code _should_ be heavily
   commented to enable use by others.
2. Applet metadata – A `dxapp.json` in the root directory. This file holds applet metadata to tell DNANexus how to build the 
   app. How this file is constructed is documented [here](https://documentation.dnanexus.com/developer/apps/app-metadata)
3. Documentation – A [github-format Markdown](https://guides.github.com/features/mastering-markdown/). This is where you 
   will find the most relevant information on what the applet does and how to use it. 

Brief instructions here on how to pull an applet from this organisation with github, and building it on the RAP
within an individual project are below.

## Cloning an Applet

If you are using a project other than that used to generate a given applet, you will need to compile this applet for your
respective DNANexus project (using the repostory for [mrcepid-filterbcf](https://github.com/mrcepid-rap/mrcepid-filterbcf.git) as an example):

1. Clone this github repo to some directory:

```commandline
git clone https://github.com/mrcepid-rap/mrcepid-filterbcf.git
```

This will create a folder named mrcepid-filterbcf, you can then:

2. Compile the source code:

```commandline
dx build -f mrcepid-filterbcf
```

the `-f` flag just tells DNANexus to overwrite older versions of the applet within the same project if it is already there.

You can then run the following to run this applet:

```commandline
dx run mrcepid-filterbcf <options>
```

## Docker Images

To provide required software to applets, we use [Docker images](https://www.docker.com/). All Docker images produced for
this and other MRC projects are available as part of Eugene Gardner's Dockerhub profile: 
[egardner413](https://hub.docker.com/u/egardner413). 

See [below](#building-a-docker-image) for specifics regarding building Docker images for specific applets. Individual 
Dockerfiles used to build images are stored in the [dockerimages repository](https://github.com/mrcepid-rap/dockerimages) 
which is part of this organisation. Individual Dockerfiles are:

| name   | Dockerfile | Associated Dockerhub Image | Brief Description |
| ------ | ---------- | -------------------------- | ----------------- |
| AssociationTesting | associationtesting.Dockerfile | https://hub.docker.com/r/egardner413/mrcepid-associationtesting | Contains software for performing rare variation burden testing |
| CADD | cadd.Dockerfile | https://hub.docker.com/r/egardner413/mrcepid-annotatecadd | Contains v1.6 distribution of CADD |
| FilterBCF | filterbcf.Dockerfile | https://hub.docker.com/r/egardner413/mrcepid-filtering | Contains software for filtering (e.g. bcftools) and VEP |

**Note:** For most of the above I have chosen **not** to hardcode programme version numbers. This means that Docker will install the latest
version of any software provided as part of the Dockerfile! The exception to this is plink/plink2 as the developers do not provide static links to
the latest version.

### Building A Docker Image

Docker images are built on an AWS Instance launched via DNANexus. I include here a brief example workflow using the "FilterBCF" Dockerfile
to enable reproducibility. Please note that uploading to dockerhub using Eugene Gardner's dockerhub account *will not work* 
as described in step 5 below. If you want to store this docker image for yourself, you will need to change the commands accordingly.
All commands are run either locally using the [dx-toolkit](https://documentation.dnanexus.com/downloads) or on a DNANexus AWS instance.

1. Launch a cloud-workstation:

```commandline
dx run app-cloud_workstation --ssh --instance-type mem1_ssd1_v2_x4 -imax_session_length=2h
```

This command will generate a series of prompts and eventually lead to a query for your DNANexus ssh password. This will then
log you into an AWS instance.

**Note:** One can adjust the amount of time the instance will exist for by changed the `max_session_length`
parameter. 2 hours should be enough to build most Docker instances.

2. Set your permissions to `root` on the instance:

```commandline
dx-su-contrib
```

3. Make and enter a fresh directory for building a Docker image and create a Dockerfile:

```commandline
mkdir dockerbuild
cd dockerbuild
```

After entering this directory, you can either copy-and-paste the text from the provided Dockerfile (using something like
`vi`), or upload the file using a combination of `dx upload`/`dx download`. Regardless of method, the following commands
require that this file is named `Dockerfile`.

4. Build the Docker image:

```commandline
docker build -t egardner413/mrcepid-filtering:latest .
```

5. Log in to dockerhub and upload the docker image:

```commandline
docker login
# Enter prompted credentials
docker push egardner413/mrcepid-filtering:latest
```

After this last command, the Docker image will be available to pull with the Dockerhub address of `egardner413/mrcepid-filtering:latest`.

# Workflows

## VCF Filtering and Rare Variant Burden Testing

The purpose of this section is to provide information on our approach to processing, filtering, annotating, and burden testing 
UK Biobank Whole Exome Sequencing (WES) data at the IMS / MRC Epidemiology Unit using the UK Biobank (UKB) Research Access 
Platform (RAP). This workflow generates a single quality-controlled data set with annotations in order to perform rare
variant burden testing analyses.

![](https://github.com/mrcepid-rap/.github/blob/main/images/RAPPipeline.png)
***Graphical Outline of Workflow***

This workflow is made up of seven individual applets. Please see the individual READMEs within these repositories for
more detailed information on what each step in the workflow does as well as accompanying source code: 

| name | repo URL | brief description |
| ---- | -------- | ----------------- |
| mrcepid-bcfsplitter | https://github.com/mrcepid-rap/mrcepid-bcfsplitter | splits bcf files into predetermined chunk sizes |
| mrcepid-filterbcf | https://github.com/mrcepid-rap/mrcepid-filterbcf | filters vcf/bcf according to predetermined method |
| mrcepid-annotatecadd | https://github.com/mrcepid-rap/mrcepid-annotatecadd | annotates filtered vcf/bcf with [CADD](https://cadd.gs.washington.edu/) |
| mrcepid-makebgen | https://github.com/mrcepid-rap/mrcepid-makebgen | convert annotated bcf files into per-chromosome bgen files |
| mrcepid-collapsevariants | https://github.com/mrcepid-rap/mrcepid-collapsevariants | Quantifies variants from a filtered vcf according to a filtering expression |
| mrcepid-buildgrms | https://github.com/mrcepid-rap/mrcepid-buildgrms | Calculate genetic relatedness matrices (GRMs) and sample exclusion lists from genetic data |
| mrcepid-runassociationtesting | https://github.com/mrcepid-rap/mrcepid-runassociationtesting | Run one of four different burden tests |

### Command-Line Examples

Eugene Gardner has generated a R Markdown notebook that contains command-line examples for running individual tools 
described as part of this workflow. This notebook also contains examples for plotting of association tests and some
quality control of QCd data. This notebook is included as a separate repository in this project:

### Provided Resources

There are three primary outputs from this workflow:

1. Filtered and annotated bgen files – generated following running "mrcepid-makebgen"
   * Stored in the folder `filtered_bgen/` in `project-G6BJF50JJv8p4PjGB9yy7YQ2`.
2. Collapsed Variant Masks for Burden Testing – generated following "mrcepid-collapsevariants"
   * Stored in the folder `collapsed_variants_new/` in `project-G6BJF50JJv8p4PjGB9yy7YQ2`.
3. Association Statistics - generated by "mrcepid-runassociationtesting"
   * Stored in the folder `results/` in `project-G6BJF50JJv8p4PjGB9yy7YQ2` 

This workflow also generates several additional resources, some of which are described here. These resources are
contained in the folder `project_resources/wes_resources/` in `project-G6BJF50JJv8p4PjGB9yy7YQ2`.

#### Combined VEP Annotations

We also provide VEP annotations for every variant in the original bcf files provided by UKBiobank:

`450k_vep.sorted.tsv.gz (file-G857Z4QJJv8x7GXfJ3y5v1qV)` in `project-G6BJF50JJv8p4PjGB9yy7YQ2`.

This file is bgzipped and tabix-indexed (`file-G857Z5jJJv8vjVyzGQ5z1b0b`) for easy querying with [tabix](http://www.htslib.org/doc/tabix.html). For the 
format of this file, please see the [outputs section of mrcepid-annotatecadd](https://github.com/mrcepid-rap/mrcepid-annotatecadd#outputs).

#### Sample Lists

We have provided sample lists of individuals specific to the 50k, 200k, and 450k releases of the UKBB WES data. These 
files have one individual ID per-line and are named like `samples_<RELEASE>.txt`.

#### Collapsed Variants

We have used mrcepid-collapsevariants to generate 16 variant masks. These masks are reasonable defaults to start performing
association testing with. They were generated by running collapsevariants on all combinations of Allele Frequency Cutoffs
and consequence annotations. Corresponding file names are included in parentheses.

Allele Frequency Cutoffs:
1. Minor Allele Frequency < 0.001% (MAF_01)
2. Allele Count = 1 (AC_1)

Consequence Annotations:
1. All synonymous variants (SYN)
2. Missense CADD ≥ 25 (MISS_CADD25)
3. Missense REVEL ≥ 0.5 (REVEL0_5)
4. Missense REVEL ≥ 0.7 (REVEL0_7)
5. Missense CADD ≥ 25 & REVEL ≥0.7 (MISS_CADD25_REVEL0_7)
6. All PTVs (PTV)
7. High confidence PTVs (HC_PTV)
8. HC_PTV + MISS_CADD25 (DMG)

# Tutorials

A tutorial on how to extract phenotype information has been provided by Alex Mörseburg. Please see the following repository
for more information:

https://github.com/mrcepid-rap/mrcepid_raptutorial

# Software

The table below contains brief information on individual applets contained within this repository. Please see the
individual READMEs within these repositories for more information on what applets are for:

| name | repo URL | native project | brief description |
| ---- | -------- | -------------- | ----------------- |
| mrcepid-collecthsmetrics | https://github.com/mrcepid-rap/mrcepid-collecthsmetrics | MRC - Y Chromosome Loss | Calculate coverage using picardtools CollectHsMetrics |
