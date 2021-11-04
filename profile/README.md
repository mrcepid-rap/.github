# Introduction

Welcome to the public organisation to store software developed by members of the Institute for Metabolic Science for the
UK Biobank [Research Access Platform (RAP)](https://dnanexus.gitbook.io/uk-biobank-rap/). The information here describes
individual software and workflows stored as repositories within this organisation.

This organisation and many of the repositories here-in are maintained by Eugene Gardner:

eugene.gardner at mrc-epid.cam.ac.uk

Other maintainers include:

T.B.D.

## Table of Contents:



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
| MRC - Y Chromosome Loss | Effects of rare variation on somatic loss of the Y-chromosome | project-G50vFK0JJjbf1VJb4gk2vVXX |
| MRC - Variant Filtering | Variant filtering and rare variation burden testing software | project-G2XK5zjJXk83yZ598Z7BpGPk |
| MRC - Miscarriage | Effects of rare homozygous LoF variation on miscarraige | t.b.d |

# Using Applets on DNANexus

When generating software/tools for the DNANexus platform, one can create either an *Applet* or an *App*. The differences
are documented in more detail [here](https://documentation.dnanexus.com/developer/apps/intro-to-building-apps), but in
brief:

* Applets are specific to a project
* Apps are visible to all projects on the DNANexus platform (with some settable restrictions by the developer).

Here, we have provided the sourcecode for applets, and thus each repository within this organisation is the source code
for an "applet". Please see the DNANexus documentation on applets to learn about how they are created, maintained, and run:

https://documentation.dnanexus.com/developer/apps.

Brief instructions here on how to pull an applet from this organisation with github, and building it on the RAP
within an individual project are below.

## Cloning an Applet

Applets have been

If you are using a project other than that used to generate the applet, you will need to compile this applet for your
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

# Workflows

## VCF Filtering and Rare Variant Association Testing

This workflow is a collection of applets that perform variant filtering of raw UK Biobank provided VCFs for the purposes
of rare variant burden testing.

![](https://github.com/mrcepid-rap/.github/blob/main/images/RAPPipeline.png)
*Graphical Outline of Workflow*



# Software

The table below contains brief information on individual applets contained within this repository. Please see the
individual READMEs within these repositories for more information on what applets are for:

| name | repo URL | native project | brief description |
| ---- | -------- | -------------- | ----------------- |
| mrcepid-collecthsmetrics | https://github.com/mrcepid-rap/mrcepid-collecthsmetrics | MRC - Y Chromosome Loss | Calculate coverage using picardtools CollectHsMetrics |
| mrcepid-filterbcf | https://github.com/mrcepid-rap/mrcepid-filterbcf | MRC - Variant Filter | Filter a bcf/vcf |
