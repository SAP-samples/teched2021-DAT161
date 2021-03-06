# Exercise 2 - Triggering the execution of a function module in a remote S/4HANA system


So far, we have missed to proof whether or not our ABAP CDS Views and the consuming SAP Data Intelligence Pipelines are really providing delta information. This would require access to the S/4HANA system in order to conduct changes on the data basis, hence on the Business Partner table or on the Sales Order object.

The Enterprise Procurement Model (EPM) demo application comes with a report that allows you to generate EPM Sales Order data. This report **`SEPM_DG_EPM_STD_CHANNEL`** can be started with the transaction **`SEPM_DG`** in S/4HANA.<br><br>
Here is an **example** of how the Report looks like in the SAP GUI:<br><br>
![](/exercises/ex2/images/ex2-000b.JPG)<br><br>

As a matter of fact, we couldn't provide an SAP GUI based access to the connected S/4HANA system. For still being able to trigger changes on EPM data in that system, we have created a variant of the above report **`SEPM_DG_EPM_STD_CHANNEL`** and a Custom ABAP Operator **`customer.teched.socreate`** which executes this report variant.<br><br>
**FYI**, the following lines in the Local Class `lcl_process`, which instantiated by the `NEW_PROCES( )` method of the Operator Class `ZCL_DHAPE_OPER_CREATE_SO`, implements this functionality:

```abap
  METHOD on_data.
    DATA lv_data TYPE string.
    mo_in->read_copy( IMPORTING ea_data = lv_data ).
    SUBMIT SEPM_DG_EPM_STD_CHANNEL USING SELECTION-SET 'SEPM_TECHED_SO' AND RETURN.
    lv_data = ' --> One additional EPM Sales Order with five related Sales Order Items created.'.
    mo_out->write_copy( lv_data ).
  ENDMETHOD.
```
<br>
You are asked to leverage this Custom ABAP Operator in this exercise with the goals to
- experience how the execution of ABAP Function Modules in a remote S/4HANA system can be triggered from SAP Data Intelligence Pipeline and
- finally have an approach to change data on the Enterprise Procurement Model in S/4HANA in order to verify the delta capabilities of the ABAP CDS Views and your Pipeline implementations.<br><br>

>**Important Note**<br>
>Please consider that we are working on the same central data basis (the EPM tables in S/4HANA) which is used by all workshop participants. Hence, Sales Order records that you create by using the Custom ABAP Operator will become change data for all session participants.
## Exercise 2.1 - Making custom ABAP Operators available in SAP Data Intelligence

After completing these steps you will have created a Pipeline that triggers the execution of a Function Module in the connected S/4HANA for EPM data generation.

1. Log on to SAP Data Intelligence and enter the Launchpad application. Then start the ***Modeler*** application.
   - Follow the link to your assigned Data Intelligence instance, e.g. https://vsystem.ingress.dh-93awrk8fy.dh-canary.shoot.live.k8s-hana.ondemand.com/app/datahub-app-launchpad/.
   - The tenant name is **"dat161"**.
   - In the next pop-up window, enter your assigned user name (e.g. ***"TA99"***) and your individual password received from the DI user registration.<br><br>
   ![](/exercises/ex2/images/ex2-002c.JPG)<br><br>
   ![](/exercises/ex2/images/ex2-002d.JPG)<br>
   
   From the Launchpad, start the ***Modeler*** application by clicking on the corresponding tile.<br><br>
   ![](/exercises/ex2/images/ex2-003b.JPG)<br><br>

2. Make sure you are in the ***Graphs*** tab of the Modeler UI (see left side). Then click the ***+*** symbol and select **Use Generation 1 Operators** in order to create a new Pipeline.<br><br>
![](/exercises/ex2/images/ex2-032b.JPG)<br><br>

3. Now a new Pipeline canvas is opened on the right side and the Modeler UI automatically switches to the ***Operators*** tab (see left side). In the list of operators, drag the ***Custom ABAP Operator*** and drop it into the Pipeline canvas. Click the ABAP CDS Reader node in the canvas one time and then click the ***configuration*** icon.<br><br>
![](/exercises/ex2/images/ex2-005b.JPG)<br><br>

4. In the configuration panel for the ***Custom ABAP Operator***, specify the S/4HANA Connection: Select `S4_RFC_TechEd`. Then click on the ***ABAP Operator*** selection icon.<br><br>
![](/exercises/ex2/images/ex2-006b.JPG)<br><br>

5. In the pop-up windows, select the **"Creation of EPM Sales Order"** option. Then click ***OK***.<br><br>
![](/exercises/ex2/images/ex2-007b.JPG)<br><br>

6. From the list of Operators, drag the ***Terminal*** operator into the Pipeline canvas. Connect **the output port of the Custom ABAP Operator with the input port of the Terminal operator**. Then connect the **output port of the Terminal operator with the input port of the Custom ABAP Operator**.<br>
With this Pipeline setup, we can - from the Terminal UI - trigger the ABAP Operator call and the reception of confirmation message at the same time.<br><br>
![](/exercises/ex2/images/ex2-008b.JPG)<br><br>

7. ***Save*** the Pipeline and enter the following parameters prompted in the pop-up windows:<br>
   - Name: `teched.XXXX.EPM_FM_Call_SO_Generator`, where XXXX is your user name, for example "teched.TA99.EPM_FM_Call_SO_Generator"
   - Description: `XXXX - Generate EPM SO data via ABAP FM call`, where XXXX is your user name, for example "TA99 - Generate EPM SO data via ABAP FM call"
   - Category: `dat161`. <br><br>
![](/exercises/ex2/images/ex2-009b.JPG)<br><br>
8.	***Run*** your Pipeline. After the Pipeline status has changed from ***pending*** to ***running***, right-click on the ***Terminal*** operator and click on ***Open UI***.<br><br>
![](/exercises/ex2/images/ex2-010b.JPG)<br><br>
9.	In the Terminal UI, put the ***cursor to the prompt line ('SDH >')*** on the lower section of the UI and press ***RETURN***. In the upper part of the UI, you can then see the confirmation from the S/4HANA ABAP Operator than one Sales Order Header record and five related Sales Order Items have been generated.<br><br>
![](/exercises/ex2/images/ex2-011b.JPG)<br><br>
***Congratilations***, you have successfully implemented a Data Intelligence Pipeline that triggers the execution of a function module in S/4HANA and allows direct interaction!
In the next section, you can leverage this remote data generation option to verify the delta capabilities of our previously built Pipelines for ABAP CDS View based data replication.<br><br>
## Exercise 2.2 - Using a custom ABAP Operator to verify your Delta Replication of EPM Sales Orders
You can now test the delta processing capabilities of the ABAP CDS View based data extraction. A nice task would be to check if Pipeline for Sales Orders replications and enrichment that you have built in [Exercise 1.4 - Extend the Pipeline for joining Sales Order with Customer data for each change in Sales Orders and persist results in S3](../ex1#exercise-13---implement-a-pipeline-for-delta-transfer-of-enhanced-epm-sales-order-data-from-s4hana-to-an-s3-object-store) is really processing the delta records from EPM in S/4HANA.<br><br>
1. In case your pipeline ***EPM_SalesOrder_Replication_Enrich_to_S3*** from exercise 1.4 is still running, you can directly go to step 5. In case you have stopped the pipeline, click on the ***Graphs*** tab of the Modeler UI (see left side). Then enter your user name in the search field (if you made your user name a part of the Pipeline names or descriptions) and start the search. You will now get a list of the pipelines that you have implemented. Click on your 'EPM_SalesOrder_Replication_Enrich_to_S3' Pipeline icon. If the displayed name is too short to recognize a unique name, just hover with your mouse over the Pipeline icons (see below).<br><br>
When searching for your pipeline, make sure you have selected the category ***dat161*** in the available categories.<br><br>
![](/exercises/ex2/images/ex2-033b.JPG)<br><br>
![](/exercises/ex2/images/ex2-012b.JPG)<br><br>
2. The Pipeline is opened in the canvas area of the Modeler UI.<br><br>
3. Make sure a Wiretap operator exists between the ***Get Header*** and ***To File*** Operator and the Sales Order to S3 Write File Operator:<br><br>
   ***Note:*** In case you have kept the Wiretap Operator between ***Get Header*** and ***Write File*** before as described in Exercise 1.4 Step 4. you can ignore this step. In case you have removed it, please add the Wiretap operator inside your pipeline and add it between the ***Get Header*** and ***Write File*** Operator.<br><br>
![](/exercises/ex2/images/ex2-013b.JPG)<br><br>
4. ***Save*** the graph and now ***Run*** it <br><br>
![](/exercises/ex2/images/ex2-028b.JPG)<br><br>
5. If you see that both of your Pipelines (data generating as well as data replication and enrichment) are in a ***running*** status, open the Wiretap UI.<br><br>
![](/exercises/ex2/images/ex2-014b.JPG)<br><br>
6. You can see now that the initial load from the ABAP CDS View in S/4HANA was successfully conducted. Please **leave the Wiretap UI open**.<br><br>
![](/exercises/ex2/images/ex2-015b.JPG)<br><br>
7. On the tab bar of the canvas area, switch to the Data Generation Pipeline and open the Terminal UI.<br><br>
![](/exercises/ex2/images/ex2-016b.JPG)<br><br>
8. Put the cursor on the prompt of the lower part (input part) of the Terminal UI and press ***RETURN***. You will see the confirmation from the ABAP Operator about the records created in the EPM demo app.<br><br>
![](/exercises/ex2/images/ex2-017b.JPG)<br><br>
9. In the Browser switch back to the ***Wiretap UI*** of your Replication and Enrichment Pipeline. You should now see that a message with new records came in, which all have the "U" indicator for Updates. This verifies the delta-enablement of the ABAP CDS View and the immediate integration with Data Intelligence.<br><br>
![](/exercises/ex2/images/ex2-018b.JPG)<br><br>
10. Now please throw one last inspecting glance at the files on S3. Here, you can again use the data browser feature in the Data Intelligence ***Metadata Explorer***. Open the application via the Launchpad.<br><br>
![](/exercises/ex2/images/ex2-019b.JPG)<br><br>
11. In the Metadata Explorer main screen, click on ***Browse Connections***.<br><br>
![](/exercises/ex2/images/ex2-020b.JPG)<br><br>
12. Click on the Connection **"TechEd_S3"** and drill further down to the folders **"DAT161"** and that of **your user** (e.g. "TA99").<br><br>
![](/exercises/ex2/images/ex2-021b.JPG)<br><br>
![](/exercises/ex2/images/ex2-022b.JPG)<br><br>
13. Browse into the specified folder inside your user folder **Enriched Sales Order**. <br><br>
    ![](/exercises/ex2/images/ex1-122b.JPG)<br><br>
    
14. Inside the folder, you will now see that a new CSV file has been generated for the changes that were triggered via the custom operator. Click on the ***More Actions*** menu (the three dots) of the newly created file - that contains the delta records - and click on ***View Fact Sheet***. <br><br>
    ![](/exercises/ex2/images/ex1-117b.JPG)<br><br>
    ***Please Note***: In case you have stopped the pipeline after exercise 1.4 and restarted it in this exercise as defined in chapter 2.2 step 4, you will see an additional file in the S3 storage that represent a second initial load of our enriched Sales Order Data. The reason for this is that the second start of the pipeline using the ***Replication*** mode has triggered an additional initial load after starting the pipeline for the second time.<br><br>
    ![](/exercises/ex2/images/ex2-034b.JPG)<br><br> 
15. You can now see that also the processing of the delta data within the Pipeline to S3 worked seamlessly. The delta records contain the enrichments and the "U" indicator that with which updates are flagged by the ABAP CDS View CDC framework.<br><br>
![](/exercises/ex2/images/ex2-035b.JPG)<br><br>

16. After checking the data that has been replicated to S3 in the Metadata Explorer, please stop your running pipeline.<br><br>
![](/exercises/ex2/images/ex2-029b.JPG)<br><br>

**Very well done!** You successfully followed the integration approach for data processing and functional execution between S/4HANA and SAP Data Intelligence and know how to realize such implementations.


## Summary
In the Deep Dive demos and in the Exercises, we have jointly worked on the implementation of delta-enabled data sources and remote functionality in S/4HANA and have leveraged these features directly in SAP Data Intelligence. We could now extend these use cases for more complex scenarios. The general implementation approaches and the support that the Data Intelligence applications provide would then still be the same.<br>
In case you have asked yourself if there are similar options for the integration with other ABAP systems such as ECC or BW, the answer is 'yes'! In these cases, you can make use of the unmodifying DMIS add-on, which provides the ABAP Pipeline Engine also for these systems.<br>
This given, you can realize real-time replication scenarios via SLT integration to SAP Data Intelligence, leverage the ODP Reader operators, and also trigger function module execution on ECC or BW systems, too.<br>
More information about general ABAP integration with Data Intelligence can be found [here](https://blogs.sap.com/2019/10/29/abap-integration-for-sap-data-hub-and-sap-data-intelligence-overview-blog/). and an additional example on how to use Custom ABAP Operator with Data Intelligence can be found [here](https://blogs.sap.com/2021/06/01/integrating-abap-function-modules-with-sap-data-intelligence/). <br><br>
**THANK YOU VERY MUCH** for having participated in this Deep Dive and Hands On workshop. We hope you have enjoyed it!<br><br>
Daniel Ingenhaag and Bengt Mertens<br><br>


*****************************************************
<br> **Table of Contents / Navigation**

- **[Overview and Getting Started](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/ex0/)**
     - [Deep Dive demos vs. Exercises](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/ex0#deep-dive-vs-exercise-sections-in-this-document)
     - [Short introduction to the Enterprise Procurement Model (EPM) in ABAP systems](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/ex0#short-introduction-to-the-enterprise-procurement-model-epm-in-sap-s4hana)
     - [Access to the exercises' Data Intelligence environment](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/ex0#access-to-the-exercises-data-intelligence-environment)

- **[Deep Dive 1 - ABAP CDS View based data extraction in SAP Data Intelligence](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/dd1/)**
    - [Deep Dive 1.1 - Create a simple ABAP CDS View in ABAP Develoment Tools (ADT)](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/dd1#deep-dive-11---create-a-simple-abap-cds-view-in-adt)
    - [Deep Dive 1.2 - Extraction and Delta enablement for simple ABAP CDS Views](https://github.com/SAP-samples/teched2021-DAT161/tree/main/exercises/dd1#deep-dive-12---extraction-and-delta-enablement-for-simple-abap-cds-views)
    - [Deep Dive 1.3 - Create a more complex ABAP CDS View in ADT (joining multiple tables)](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/dd1#deep-dive-13---create-a-more-complex-abap-cds-view-in-adt-joining-multiple-tables)
    - [Deep Dive 1.4 - Delta-enablement for complex ABAP CDS Views (joining multiple tables)](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/dd1#deep-dive-14---delta-enablement-for-complex-abap-cds-views-joining-multiple-tables)
    - [Deep Dive 1.5 - Integrate ABAP CDS Views in SAP Data Intelligence Pipelines](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/dd1#deep-dive-15---integrate-abap-cds-views-in-sap-data-intelligence-pipelines)
    
- **[Deep Dive 2 - Calling an ABAP function module in SAP S/4HANA from SAP Data Intelligence](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/dd2/)**
    - [Deep Dive 2.1 - Create a custom ABAP Operator in SAP S/4HANA](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/dd2#deep-dive-21---create-a-custom-abap-operator-in-sap-s4hana)
    - [Deep Dive 2.2 - Integrate the custom ABAP Operator in a SAP Data Intelligence Pipeline](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/dd2#deep-dive-22---integrate-the-custom-abap-operator-in-a-sap-data-intelligence-pipeline)
    
- **[Exercise 1 - Replicating data from S/4HANA ABAP CDS Views in SAP Data Intelligence](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/ex1/)**
    - [Exercise 1.1 - Consume the EPM Business Partner ABAP CDS Views in SAP Data Intelligence](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/ex1#exercise-11---consume-the-epm-business-partner-abap-cds-views-in-SAP-Data-Intelligence)
    - [Exercise 1.2 - Extend the Pipeline to transfer the Customer data into a HANA Cloud Database with Initial Load mode](https://github.com/SAP-samples/teched2021-DAT161/tree/main/exercises/ex1#exercise-12---extend-the-pipeline-to-transfer-the-customer-data-into-a-hana-cloud-database-with-initial-load-mode)
    - [Exercise 1.3 - Implement a Pipeline for delta transfer of enhanced EPM Sales Order data from S/4HANA to an S3 Object Store](https://github.com/SAP-samples/teched2021-DAT161/tree/main/exercises/ex1#exercise-13---implement-a-pipeline-for-delta-transfer-of-enhanced-epm-sales-order-data-from-s4hana-to-an-s3-object-store)
    - [Exercise 1.4 - Extend the Pipeline for joining Sales Order with Customer data for each change in Sales Orders and persist results in S3](https://github.com/SAP-samples/teched2021-DAT161/tree/main/exercises/ex1#exercise-14---extend-the-pipeline-for-joining-sales-order-with-customer-data-for-each-change-in-sales-orders-and-persist-results-in-s3)
    
- **[Exercise 2 - Triggering the execution of a function module in a remote S/4HANA system](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/ex2/)**
    - [Exercise 2.1 - Making custom ABAP Operators available in SAP Data Intelligence](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/ex2#exercise-21---making-custom-abap-operators-available-in-sap-data-intelligence)
    - [Exercise 2.2 - Using a custom ABAP Operator to verify your Delta Replication of EPM Sales Orders](https://github.com/SAP-samples/teched2021-DAT161/edit/main/exercises/ex2#exercise-22---using-a-custom-abap-operator-to-verify-your-delta-replication-of-epm-sales-orders)
