# About # 

Implementing MultiTenancy has lots of value -- but does require some forthought to get right.

## Resource Tenancy ##

The Tenancy is determined from the Resource URL segment:

* https://{tenancy}.{system}.{org}.{tld}/{module}/{resource}?{constraints}


## Permissions ##

Access to a Resource by a Principal is determined by Permission's set on the Operation.

## Prerequisites ##
* A Principal can be a member of n Tenancies.
* A Principals has Permissions specific to a Tenancy.
* A SuperUser is a Member of all Tenancies 
* [TODO:THINK] A SuperUser has lots of Permissions, but not All.


## Implications ##

* An Member of TenancyA who has not been invited to TenancyB is only a Public Usesr in TenancyB,
* Until invited into TenancyB
* An Principal with any Role (eg: Admin) of TenancyA can be invited to another Tenancy (TenancyB) as another Role (User) with more or less rights.
* A SuperUser can be invited to a Tenancy, so that when the SuperUser role is removed from the Principal, it reverts back to simple membership.

* At signin:
  * A Claim response created by an IdP will contain a UserId
  * That can be mapped to a Principal Id
  * The user's roles for the current Tenancy are embedded in the claims to be returned to the user
  * A Session Record is started for this person 
    * actually, the user's anon credential is updated to point to the new Principal, as oppossed to the custom Anonymous record)

  
## To Resolve ##

  