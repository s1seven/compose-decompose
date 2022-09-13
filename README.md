# BDD Specification

```gherkin
Scenario: Create an asset of discrete parts
Given a Mill certificate with 100 tubes each 50kg, 1.7kg CO2 per kg, recycling share 10% and 100% renewables share
When the issuer creates a transaction
Then created asset has the attributes
    Asset.amount = 100
    Asset.unit_of_measure = pcs (enum :   pieces, volume, weight, area) 
    Asset.mass.quantity = 50 (integer for pieces, number for everything else)
    Asset.mass.quantity_unit = kg (enum or string ? ) 
    Asset.PCF.quantity = 1.7 (number)
    Asset.PCF.quantity_unit = kg CO2e/kg
    Asset.RecyclingShare = 0.1 (a number between 0 and 1)
    Asset.RenewableShare = 1 (a number between 0 and 1)
Then the issuer should be the asset owner
Then the transaction metadata should contain the schema reference used to describe the asset
```

```gherkin
Scenario: Create an asset of loose material
Given a CoA of 10,000 kg PPE granulate with 400kg CO2, recycling share 50%, and no renewables share packed on one truck.
When the issuer creates a transaction
Then created asset has the attributes
    Asset.amount = 10,000,000
    Asset.unit_of_measure = g
    Asset.PCF.quantity = 0.04
    Asset.PCF.quantity_unit = kg CO2e/kg
    Asset.RecyclingShare = 0.5 
    Asset.RenewableShare = 0
Then the issuer should be the asset owner	
Then the transaction metadata should contain the schema reference used to describe the asset
```

```gherkin
Scenario: Create an asset of loose material packaged
Given a CoA of 20,000 kg PPE granulate with 500kg CO2, recycling share 50%  packed in 50kg bags.
When the issuer creates a transaction
Then created asset has the attributes
    Asset.amount = 400
    Asset.unit_of_measure = pcs (enum :   pieces, volume, weight, area)  see ref
    Asset.mass.quantity = 50 (integer for pieces, number for everything else)
    Asset.mass.quantity_unit = kg (enum or string ? ) 
    Asset.PCF.quantity = 0.025 (number)
    Asset.PCF.quantity_unit = kg CO2e/kg
    Asset.RecyclingShare = 0.5 (a number between 0 and 1)
Then the issuer should be the asset owner
Then the transaction metadata should contain the schema reference used to describe the asset
```

```gherkin
Scenario: Transfer parts to another party
Given an asset with attributes
    Asset.amount = 100
    Asset.unit_of_measure = pcs
    Asset.mass.quantity = 50 
    Asset.mass.quantity_unit = 
    Asset.PCF.quantity = 1.7 
    Asset.PCF.quantity_unit = kg CO2e/kg
    Asset.RecyclingShare = 0.1 
    Asset.RenewableShare = 1
When the owner creates a transaction transferring 50 pcs to other party
Then the owner owns 50 pcs of the asset
Then the other party owns 50 pcs of the asset
```

```gherkin
Scenario: Transfer lose material to other party
Given an asset with attributes
    Asset.amount = 10,000,000
    Asset.unit_of_measure = g
    Asset.PCF.quantity = 0.04
    Asset.PCF.quantity_unit = CO2e/kg
    Asset.RecyclingShare = 0.5 
    Asset.RenewableShare = 0
When the owner creates a transaction transferring 1,000 kg to another party
Then the owner owns 9,000 kg 
Then the other party owns 1,000 kg
```

```gherkin
Scenario: Transfer packages of loose material
Given an asset with 
    Asset.amount = 100
    Asset.unit_of_measure = pcs
    Asset.mass.quantity = 50 
    Asset.mass.quantity_unit = 
    Asset.PCF.quantity = 1.7 
    Asset.PCF.quantity_unit = kg CO2e/kg
    Asset.RecyclingShare = 0.1 
    Asset.RenewableShare = 1
When the owner creates a transaction transferring 50 pcs to another party
Then the owner owns 50 pcs of the asset
Then the other party owns 50 pcs of the asset
```

```gherkin
Scenario: Create a new asset from many inputs
Given asset steel with
    Asset.amount = 5,000,000
    Asset.unit_of_measure = weight
    Asset.mass.quantity = 5,000.00
    Asset.mass.quantity_unit = kg
    Asset.PCF.quantity = 0
    Asset.PCF.quantity_unit = kg CO2e/kg
Given asset coal with
    Asset.amount = 5,000,000
    Asset.unit_of_measure = weight
    Asset.mass.quantity = 5,000,000.00
    Asset.mass.quantity_unit = g
    Asset.PCF.quantity = 500
    Asset.PCF.quantity_unit = kg CO2e/kg
Given asset ore with
    Asset.amount = 5,000,000
    Asset.unit_of_measure = weight
    Asset.mass.quantity = 5,000,000.00
    Asset.mass.quantity_unit = g
    Asset.PCF.quantity = 500
    Asset.PCF.quantity_unit = CO2e/kg
Given asset power with
    Asset.amount = 1,000,000
    Asset.unit_of_measure = Wh
    Asset.PCF.quantity = 500
    Asset.PCF.quantity_unit = kg CO2e
    Asset.RenewableShare = 50%
When the owner triggers a COMPOSE transaction with the inputs
Then a new asset is created with
    Asset.amount = 10,000,000
    Asset.unit_of_measure = weight
    Asset.mass.quantity = 10,000,00.00
    Asset.mass.quantity_unit = g
    Asset.PCF.quantity = 0.6
    Asset.PCF.quantity_unit = kg CO2e/kg
Then the assets steel, coal, ore, and power cannot be used in any further transaction
Then it should be traceable that the output is created from the inputs
```

```gherkin
Scenario: Create products from material with residual scrap
Given an asset with 
    Asset.amount = 100,000
    Asset.unit_of_measure = weight
    Asset.mass.quantity = 100,000 
    Asset.mass.quantity_unit = g
    Asset.PCF.quantity = 500
    Asset.PCF.quantity_unit = kg CO2e
When the owner creates a transaction creating products
Then a new asset is created
    Asset.amount = 4
    Asset.unit_of_measure = pcs
    Asset.mass.quantity = 20,000 
    Asset.mass.quantity_unit = g
    Asset.PCF.quantity = 100
    Asset.PCF.quantity_unit = kg CO2e 
Then the existing asset changes to
    Asset.amount = 20,000
    Asset.unit_of_measure = weight
    Asset.mass.quantity = 20,000 
    Asset.mass.quantity_unit = g
    Asset.PCF.quantity = 100
    Asset.PCF.quantity_unit = kg CO2e
```

```gherkin
Scenario: Create products from material assigning PCF to residual scrap
Given an asset with 
    Asset.amount = 100,000
    Asset.unit_of_measure = weight
    Asset.mass.quantity = 100,000 
    Asset.mass.quantity_unit = g
    Asset.PCF.quantity = 500
    Asset.PCF.quantity_unit = kg CO2e
When the owner creates a transaction creating products
Then a new asset is created
    Asset.amount = 4
    Asset.unit_of_measure = pcs
    Asset.mass.quantity = 20,000 
    Asset.mass.quantity_unit = g
    Asset.PCF.quantity = 20
    Asset.PCF.quantity_unit = kg CO2e 
Then a new asset is created with
    Asset.amount = 20,000
    Asset.unit_of_measure = weight
    Asset.mass.quantity = 20,000 
    Asset.mass.quantity_unit = g
    Asset.PCF.quantity = 420
    Asset.PCF.quantity_unit = kg CO2e

```
