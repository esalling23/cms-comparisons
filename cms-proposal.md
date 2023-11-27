# CMS Proposal

This document outlines considerations & proposed tooling for the integration of a CMS for handling static content in the "pre-login" Swan games site.

As a side-goal, we want to remove the need to mediate connectivity when navigating between the pre-login and game sites. The first step would be to migrate the pre-login site, as-is, over to the react-native-frontend app. From there, we will consider the proper CMS tool for the pre-login content.

## Goals

The requirements for a reasonable tool for our CMS: 
- easy to use content editor for non-technical members of the team
- can be integrated with a react frontend
- commonly used & well documented for future work
- free
- low(er) hosting costs

### Considerations

**Headless**

A headless CMS means that the CMS is just the "backend" that manages the database content. This means we have full control over the "frontend" which will be our react-native application, pulling content from the CMS (faqs, announcements, etc.) to display for users.

---

**Node vs Flask**

Longterm, the team may consider using NodeJS + Express in favor of Flask entirely for performance reasons. ([see](https://python.plainenglish.io/lets-find-which-is-fast-node-js-vs-flask-python-5ab68b3c3559) [comparisons](https://www.diva-portal.org/smash/get/diva2:1669487/FULLTEXT01.pdf)) 
For this reason I will propose some tools that can be integrated with NodeJS rather than Flask. 

---

**Hosting**

A headless CMS removes the need to go through a separate service for hosting. All hosting will remain on AWS managed by us. 

The front-end will be integrated with the existing React Native app instance, so there will be no additional hosting required. 

For the admin editor "back-end", some CMS options require a separate server running (see: Strapi, Directus), while others are built on top of existing servers (see: KeystoneJS, PayloadCMS).

In order to reduce costs, we may consider using a code-based CMS that can be integrated with an existing service (see above: node). This way no additional hosting container instances would be required. 

---

**Database**

Should the CMS database be separate from the game database?

- Will putting the data in 1 large db save $? Or vise-versa?
- What is higher priority: separation or cost?

Why do we care: If the database should be combined into 1 mySQL instance, then we should choose a CMS that supports mySQL.

---

**Data Building**

Do we have a need for non-developers to create data structures? 

For example: We need to add the FAQ page, which will have a list of "FAQs" that have a "question", "answer", and "see-more" link. Who would be in charge of defining that data structure? 

Both Strapi & Directus allow for in-editor, no-code data structure creation. 

---

**Layout Flexibility**

Without using a site-builder, we can still introduce features to make future design and layout updates easier. 

The CMS tooling should support some level of reordering, reorganizing, and relaying out page content. 

---

## The Top Contenders

### Option: [Directus](https://directus.io/)

![directus local screenshot](https://res.cloudinary.com/esalling/image/upload/v1699645320/cms/Screen_Shot_2023-11-10_at_2.38.37_PM.png)

- Mature project: v9
- Supports various SQL databases

Bonus
- Offers paid hosting via Directus Cloud
- Built-in internationalization, media library, analytics dashboards, branding, trigger flows, and more
- Content type editor for no-code data modeling
- Supports data backups 

Considerations
- "If you use Directus for anything in a company with over 5M revenue (not profit), you'll have to get enterprise licence even if you self host." (quote from [reddit](https://www.reddit.com/r/webdev/comments/xphtbk/comment/k59r1k9/?utm_source=share&utm_medium=web2x&context=3)) [see self hosting pricing page](https://directus.io/pricing/self-hosted)
![pricing sheet](directus_pricing.png)

#### Walkthrough App Example

![directus walkthrough](directus_walkthrough.mov)

![frontend directus](directus_fe.png)

### Option: [KeystoneJS (v6)](https://keystonejs.com/)

![keystone local ss](https://res.cloudinary.com/esalling/image/upload/v1699645291/cms/Screen_Shot_2023-11-10_at_2.34.52_PM.png)

KeystoneJS is a highly customizable headless CMS option that supports several different SQL databases to store content. It is built with NodeJS.

- Mature project: v6
- Supports various SQL databases.

Bonus
- Automated admin UI built based on content type structure definitions
- [Rich document field editor](https://keystonejs.com/docs/guides/document-field-demo) to allow for fairly flexible page-builder-like editing

Considerations
- Does not support internationalization at this time, though there are plans to have it added
- No built-in media library support (though does have S3 integration)
- Custom components can be built in but require some dev work


#### Walkthrough App Example

![keystone walkthrough](keystone.mov)

![frontend keystone](keystone_fe.png)

---

## Other Options

### Option: [Strapi](https://strapi.io/)

![strapi local ss](https://res.cloudinary.com/esalling/image/upload/v1699645319/cms/Screen_Shot_2023-11-10_at_2.38.16_PM.png)

Strapi is the most popular headless CMS tool for developers.

- Mature project: v4
- Supports various SQL databases
  
Bonus
- Has paid options for bonus features, and offers paid hosting
- Content type editor for no-code data modeling
- Very customizable, including admin customization baked in
- Community marketplace and plugins available to extend features including adding content preview
  
Considerations: 
- Limited features on free tier
- Relies on the Strapi servers which some users note can go down unexpectedly or be slow to load

### Option: Headless Wordpress

Usually Wordpress handles both the CMS & the frontend display of that managed content. A [headless solution](https://snipcart.com/blog/headless-wordpress) could support our needs for a commonly used tool with great developer support while also keeping our frontend in ReactJS. 

- Very mature project with large dev community 
- Supports various databases including MySQL

Bonus
- Headless offers better security handling than regular Wordpress
- Built-in content type builder, including rich text and other complex fields.

Considerations: 
- Headless means a lot of the reasons to use wordpress are gone, ex: live content preview
- Less customizable: locked into Wordpress's editor
- Potentially slower
- Without additional packages, will require some knowledge of PHP development

### Option: [~~PayloadCMS~~](https://payloadcms.com/)

**Removed from consideration**

![payload local ss](https://res.cloudinary.com/esalling/image/upload/v1699645319/cms/Screen_Shot_2023-11-10_at_2.40.25_PM.png)

- Newer project: v2
- Supports MongoDB & Postgres dbs (not mySQL)

Bonus
- Can support content preview, internationalization, and more

Considerations
- Admin editor UI built with React, and requires dev work for modifications

## Recommendation

**Major consideration**: separate or shared service? 

Shared service: Payload or KeystoneJS

Both of these tools can be integrated into the existing node server to be hosted on that service.

| Tool | Reason to use | Reason NOT to use |
|------|---------------|-------------------|
| PayloadCMS | More out of the box features. | Requires coding admin UI components (ReactJS). Does not support MySQL at this time |
| KeystoneJS | Admin UI is generated automatically based on object-style data definitions. | More bare-bones from the start. |

Separate service: Strapi, Directus, Wordpress

Both of these tools will require a separate container instance to run a separate server. 

| Tool | Reason to use | Reason NOT to use |
|------|---------------|-------------------|
| Strapi | Very non-developer friendly interface. Potentially better large-scale production performance. | Must be started from scratch with an empty database. Environment migrations are not straight-forward. App reloads when building content types for migrations, which can slow down development time. |
| Directus | More feature rich and better implemented based on developer community experience. Can be added on top of an existing database. | Database-first approach can have it's own headaches. |

---

**Major consideration**: shared or separate database instance? 

If the database will be shared with the existing game MySQL database, we will have to take PayloadCMS out of the running. 

### Closing Thoughts

A code-based option seems to offer better long-term flexibility, especially if we want to leave open the option to use the CMS for managing post-login game data as well. 

I suggest **KeystoneJS** for the following reasons: 
- More mature project than PayloadCMS, with dedicated team and large dev community
- Admin UI is auto-generated so there's no need to build out the admin editor views
  - Also supports customizing the admin if necessary
- Layout & content block "page-builder" style editor field
- Supports MySQL so we could have the option of using a single database for pre & post login
- Many users toat the power of Prisma in v6 for database management

If we prefer a standalone option ([Strapi or Directus](https://medium.com/@weframe.tech/strapi-vs-directus-which-one-is-good-ff398e7c9e42#:~:text=Directus%20extends%20its%20support%20to,MySQL%2C%20MariaDB%2C%20and%20SQLite.)) I would recommend **Directus** for the following reasons: 
- Offers analytics and developer flow triggers
- Is a more generic data management framework (similar to phpMyAdmin) and so offers more flexibility
- More popular by folks using it in production
  - Lots of people saying they started on Strapi and moved to Directus for various reasons, especially when moving into production stage
- Database-first, so no need for restarts or migrations during content development
