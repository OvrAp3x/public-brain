---
{"dg-publish":true,"permalink":"/software/atlassian-jira-assets-insights/"}
---


Jira assets or formerly known as "Insights" (used to be a plugin, but was bought by atlassian).

Is a acustomizable asset management system that integrates in jira.
It requires [[Atlassian Jira servicemanagement\|Atlassian Jira servicemanagement]] Premium licensing though.

Other than that it is a great tool for modelling assets, computers/systems/software, even business processes and GDPR inventory objects.


## AQL /( IQL)

Asset Query Language /Insight Query Language is the way that assets can be queried.
atlassian has a reference page here; https://support.atlassian.com/jira-service-management-cloud/docs/use-insight-query-language-iql/

## AQL snippets

Search for multiple objectypes
`objectType IN ("System","Services") 

Search for empty field
`"Asset classification" IS EMPTY