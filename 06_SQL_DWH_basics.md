# 1. SQL  DWH  DW.Star basics

## 1.1 Basic understanding of the DB

Firstly we should understand that the database is seperated into 2 main categories:

* Technical Data (Dim tables)
* Measurements (Fact tables)

This means if we are looking for data with different timestamps we should use the table FactKrivuljeNMC in case we need the location or if the metering point is using solar power we should look into the table DimMerilnaMesta.

## 1.2 SOM vs. NMC tables

SOM tables indicate that the measurements are on the transformator level and NMC indicates Low voltage measurements. example:

* FactKrivuljeNMC will have measurements of metering points
* FactKrivuljeSOM will have measurements of transformator stations 

in some cases we can also see DimDogodekStevec where stevec means metering point but we have a different table DimDogodekSOM for transformator stations events.

## 1.3 Understanding DimMerilnoMesto Table

First we need to know that we have about 91.000 - 92.000 metering points, but the table returns 600.000+ rows so let us take a look what is good to know here (similar practices apply to some other tables).

First let's take a look at **MerilnoMestoAktivno** which has a boolean Data type and tells us if the metering point is active:

**WHERE MerilnoMestoAktivno = 1** -verifies that the metering point is active

We have 2 columns MerilnoMestoSID and MerilnoMestoUID, MerilnoMestoSID is unique and is basically the contract ID. This tells us that MerilnoMestoUID can be in more rows under different MerilnoMestoSIDs. We can take a look under the column **veljavnost_spremembe_od** and **veljavnost_spremembe_do** to see if the contract is still valid:

* AND [veljavnost_spremembe_od] < GETDATE() -validity of contract
* AND [veljavnost_spremembe_do] > GETDATE() -validity of contract

Etl processes are also used beforehand on the data so we have to take this into account:

* AND EtlDeleted = 0   -makes sure that rows is valid
* AND EtlInsertDate < GETDATE() -Check that the data inserted is older than the current date
* AND EtlUpdateDate < GETDATE() -Check that the data update is older than the current date

to be continued
