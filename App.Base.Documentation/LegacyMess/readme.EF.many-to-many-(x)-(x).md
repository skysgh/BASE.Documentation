# About #

## Key Design Points ##

* (Principal) has a Collection Navigation Property (ICollection<Tags>)
* (PrincipalTag) has no FK or Entity Navigation Property.
* The relationship is defined on (Principal)
  * But I'd define it its own file as it will end up being it's own Junction Table
* The HasMany(...) defines the Collection Navigation Property on (Principal)
* Remember that the syntax is crappy, but there it is...you define the relationship on only the Role that has the Collection (ie, (Principal)).

* The WithMany(...) defines the (optional) Collection Navigation Property on (Tags)
  * In this case, we're not defining a return Navigation Property.
  * See the other example ('(x)-(x)-both-ways') for more on that.


## Example ##


The following is the bare minimum to create a Junction Table between two Entities:

modelBuilder.Entity<Principal>() 
    .HasMany(t => t.Tags) 
    .WithMany()

But this creates a Junction Table and column names with default values. A bit messy.

The following defines a parent object that has an optional set of Tags
defining the name of the Junction Table and its columns.

        modelBuilder.Entity<Principal>()
            .HasMany(p => p.Tags)
            .WithMany()
            .Map(x =>
            {
                x.ToTable(typeof(Principal).Name + "__" typeof(Tag).Name);
                x.MapLeftKey("PrincipalFK");
                x.MapRightKey("TagFK");
            });



	public class Principal {
		...
		public virtual ICollection<PrincipalTag> Tags
        {
            get
            {
                if (this._tags == null)
                {
                    this._tags = new Collection<PrincipalTag>();
                }
                return this._tags;
            }
            set => this._tags = value;
        }
        private ICollection<PrincipalTag> _tags;
		...
	}

	public class PrincipalTag {
		public Guid Id {get;set;}
		...
	}



THe same applies, whether we are talking about Roles in a simple system,
where 
* multiple Roles are assigned to multiple Users.
* Users have a Collection property of Roles
* Roles do not have a Collection property of Users to whom the Role has been applied.


            modelBuilder.Entity<Principal>()
                .HasMany(s => s.Roles)
                .WithMany()
                .Map(x =>
                {
	                x.ToTable(typeof(Principal).Name + "__" typeof(Role).Name);
                    x.MapLeftKey("PrincipalFK");
                    x.MapRightKey("RoleFK");
                });
        }


### TroubleShoooting ###
* Remember that the syntax is crappy, but there it is...you define the relationship on only the Role that has the Collection (ie, (Principal)).

* If the relationship is both ways (ie there is a Collection on both objects pointing to each other)
  Refer to the other example ('(x)-(x)-both-ways') where an example is given on how to use 
  `.WithMany(x=>x.OtherCollection)`  on *only one model definition*.



## Resources ##

* http://www.entityframeworktutorial.net/code-first/configure-many-to-many-relationship-in-code-first.aspx
* https://lostechies.com/jimmybogard/2014/03/12/avoid-many-to-many-mappings-in-orms/