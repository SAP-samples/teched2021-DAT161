# DAT161 - Integrating SAP S/4HANA into SAP Data Intelligence: Deep Dive and Hands On

## Description

This repository contains the material for the SAP TechEd 2021 session called
DAT161 - Integrating SAP S/4HANA into SAP Data Intelligence: Deep Dive and Hands On.

## Overview

SAP Data Intelligence is SAP's leading product for data integration, data management, intelligent processing and orchestration across different (hybrid) data environments. One important integration aspect is the replication, consumption, and exchange of data and functionality with SAP's core Intelligent Enterprise and Business Suite systems. The ABAP integration capabilities in SAP Data Intelligence open seamless, "any-premise" interoperability with almost all the features available in these environments.

This hands-on session will focus on connectivity and system interoperability to SAP S/4HANA systems and provides an insight on how to use the corresponding out-of-the-box operators in SAP Data Intelligence to smoothly integrate with the above SAP Business applications and beyond.

## Requirements

The requirements to follow the exercises in this repository are:

- Basic knowhow about the usage and scope of S/4HANA
- General understanding of Enterprise Data Management and ETL (Extraction, Transformation, Load)
- Basic programming skills in ABAP are of advantage

## Deep Dive Demos and HandsOn Exercises
The main use cases behind this workshop are

- The extraction the Business Partner master data in S/4HANA's demo application Enterprise Procurement Model (EPM) and making the records available for the corporate Data Analysts in SAP HANA Cloud database.
- Also persist the transactional data in S3, i.e for EPM Sales Order objects which are built from joins over multiple EPM tables.
- In both cases, any single change of these data sources in the S/4HANA system has to be instantly and automatically replicated to the related files in S3.
- Additionally, the Sales Order data have to be enriched with Customer master data, for the initial load and then on every change committed to the EPM Sales Order data in S/4HANA.
- In order to create test changes on the EPM Sales Order data without having access to the SAP GUI in the S/4HANA system, (a variant of) the EPM Data Generator reports needs to be executed as an ABAP Function Modul call from a Data Intelligence Pipeline.

The Deep Dive live demos and the hands-on Exercise contents reflect the above scenarios and provide a consecutive approach for their realization. But other than during the on-site TechEd events in the past years, it was not feasible to provide the Eclipse based ABAP Development Tools (ADT) and the SAP GUI to our participants in the this year's virtual version of the TechEd.

Since it is a goal of this workshop to show and get the hands on the complete end-to-end implementation processes of ABAP integration with SAP Data Intelligence, all parts of this session that require these (local) applications will be presented as live Deep Dive demos, conducted in ADT and in SAP S/4HANA by the trainer, the Exercises - in opposite - are then performed by the participants in SAP Data Intelligence and will leverage those artifacts in S/4HANA that got created during the Deep Dive demos.

## Exercises

Provide the exercise content here directly in README.md using [markdown](https://guides.github.com/features/mastering-markdown/) and linking to the specific exercise pages, below is an example.

- [Getting Started](exercises/ex0/)
- [Exercise 1 - First Exercise Description](exercises/ex1/)
    - [Exercise 1.1 - Exercise 1 Sub Exercise 1 Description](exercises/ex1#exercise-11-sub-exercise-1-description)
    - [Exercise 1.2 - Exercise 1 Sub Exercise 2 Description](exercises/ex1#exercise-12-sub-exercise-2-description)
- [Exercise 2 - Second Exercise Description](exercises/ex2/)
    - [Exercise 2.1 - Exercise 2 Sub Exercise 1 Description](exercises/ex2#exercise-21-sub-exercise-1-description)
    - [Exercise 2.2 - Exercise 2 Sub Exercise 2 Description](exercises/ex2#exercise-22-sub-exercise-2-description)



**OR** Link to the Tutorial Navigator for example...

Start the exercises [here](https://developers.sap.com/tutorials/abap-environment-trial-onboarding.html).

**IMPORTANT**

Your repo must contain the .reuse and LICENSES folder and the License section below. DO NOT REMOVE the section or folders/files. Also, remove all unused template assets(images, folders, etc) from the exercises folder. 

## How to obtain support

Support for the content in this repository is available during the actual time of the online session for which this content has been designed. Otherwise, you may request support via the [Issues](../../issues) tab.

## License
Copyright (c) 2021 SAP SE or an SAP affiliate company. All rights reserved. This project is licensed under the Apache Software License, version 2.0 except as noted otherwise in the [LICENSE](LICENSES/Apache-2.0.txt) file.
