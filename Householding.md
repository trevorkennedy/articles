# Householding Complexity

## Competing Definitions

Although the concept of a household is pretty straight-forward, in reality the term is quite nebulous. This is an important consideration because many statistics used in marketing and data science are only available at the household level. Meanwhile, businesses typically collect transactional information at another level (i.e. by individual customer) and matching this data to the appropriate household in not trivial.

### Merriam-Webster

The Merriam-Webster dictionary defines [household](http://www.merriam-webster.com/dictionary/household) as:

> The people in a family or other group that are living together in one house

### U.S. Census Bureau
The Census Bureau, however, provides a much more detailed definition of "household":

#### Household
> A household consists of all the people who occupy a housing unit. A house, an apartment or other group of rooms, or a single room, is regarded as a housing unit when it is occupied or intended for occupancy as separate living quarters.

> A household includes the related family members and all the unrelated people, if any, such as lodgers, foster children, wards, or employees who share the housing unit. A person living alone in a housing unit, or a group of unrelated people sharing a housing unit such as partners or roomers, is also counted as a household. 

The Census Bureau further defines two major [categories](http://www.census.gov/cps/about/cpsdef.html) of households: "family" and "nonfamily".

#### Family household
> A family household is a household maintained by a householder who is in a family, and includes any unrelated people who may be residing there. 

#### Nonfamily household
> A nonfamily household consists of a one-person households and where the householder shares the home exclusively with people to whom he/she is not related.

### Epsilon TotalSource Plus

[Epsilon](http://www.epsilon.com/what-we-do/marketing-data/totalsource-consumer-data/), a leading vendor of consumer marketing data, provides a lightly different definition:

#### Household

> A household is a unique surname at an address point (postal address). If the same surname is present we will carry all available names up to 5 individuals within a household.

The unique name aspect of their methodology can be troublesome for married households because a recent study by the [New York Times](http://www.nytimes.com/2015/06/28/upshot/about-the-maiden-name-analysis.html) suggests that only 73.5% of women changed their surnames after marriage.

#### Head of household

Determining the primary householder is also tricky:

> The primary name is determined using a number of business rules: by looking at the verification date, number of sources, and types of sources contributing to each given name within the household.

### Added Complexity

In addition to the fact that more than 25% of women decide to keep their maiden names after marriage, there is also a rise in the number of adults who life with roommates in non-family households. Research by [Zillow](http://www.zillow.com/blog/doubling-up-161820/) finds that 32% of adults live with roommates.

![Adults living with roommates](http://cdn2.blog-media.zillowstatic.com/1/PR_Doubling-Up_Blog-and-Twitter-50cad6.png)

The number of multi-generational households (adult children living with their parents) is also increasing. A 2012 study by the Pew Research Center shows that 21.6% of adults aged 25-34 live with a parent.

![Share of young adults in multi-generational households](http://www.pewsocialtrends.org/files/2012/03/sdt-2012-03-05-boomerang-00-02.png "Share of young adults in multi-generational households")