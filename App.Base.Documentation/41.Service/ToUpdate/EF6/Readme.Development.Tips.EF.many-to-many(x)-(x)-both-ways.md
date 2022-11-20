# About #

You can have a many to many where (Person) has (Role), and that only (Person) has a Collection Navigation Property, 
as described elsewhere.

But if you want the relationship to be both ways, where (Person) has a COllection of (Role) and (Role) has a Collection of (Person)
(not that that is a good idea in the case of Roles as there will be too many (Person) in the collection!) it is nearly
the same, just have to use the WithMany


## Key Design Points ##

* It's shitty syntax, but there you have it: although we are defining the (Principal) model, you are also defining
  the Collection property on the other Model.
* Do NOT define the Collection on the (Role) object...that duplication will lead to error messages about Cascade Delete
  due to two relationships being set up (TODO: something like that, describe better, using 
  https://stackoverflow.com/questions/29062094/entity-framework-code-first-cycles-or-multiple-cascade-paths)



## Implementation Example ##

            modelBuilder.Entity<Principal>()
                .HasMany(s => s.Roles)
				// This is the key difference
                .WithMany(x => x.Principals)
                .Map(x =>
                {
	                x.ToTable(typeof(Principal).Name + "__" typeof(Role).Name);
                    x.MapLeftKey("PrincipalFK");
                    x.MapRightKey("RoleFK");
                });
        }


But here, the issue is it causes CascadingDelete errors, in that if you delete records from the Map table, it would delete the parent Role and Principal.




## Resources ##
* https://www.experts-exchange.com/questions/28975728/Entity-Framework-many-to-many-relationship-not-getting-set-up-in-code-first-approach.html
* http://www.entityframeworktutorial.net/code-first/configure-many-to-many-relationship-in-code-first.aspx
* https://lostechies.com/jimmybogard/2014/03/12/avoid-many-to-many-mappings-in-orms/