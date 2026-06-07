# Vacation Tracking system VTS
## This repositroy provide an implementation example for a dynamic Vacation Tracking System designed for multi-manager organizational structures. This system enables employees to manage their leave and time-off requests while providing managers with a centralized view to track and schedule leaves across multiple projects and cross-functional teams.

The project provide the following functionalities :
- Implements a flexible rules-based system for validating and verifying leave time requests.
- Enables employees to manage their vacation time.
- Enables manager approval (optional).
- Provides access to requests for the previous calendar year, and allows requests to be made up to a year and a half in the future.
- Uses e-mail notification to request manager approval and notify employees of request status changes.
- Uses existing hardware and middleware.
- Is implemented as an extension to the existing intranet portal system, and uses the portal's single-sign-on mechanisms for all authentication.
- Keeps activity logs for all transactions.
- Enables HR personnel to enter and update employee vacation data in the system.
- Enables the HR and system administration personnel to override all actions restricted by rules, with logging of those overrides.
- Allows managers to directly award personal leave time (with system-set limits).
- Provides a Web service interface for other internal systems to query any given employee’s vacation request summary.
- Interfaces with the HR department legacy systems to retrieve required employee information and changes

## Core Engineering Challenges
- handle the complex business logic of validating vacation request dates and employee balances.
- building an efficient data model to store users and vacation requests data.
- implement role-based security for employees and managers.

## Watch the full series!

<a href="https://www.youtube.com/playlist?list=PLSvCAHoiHC_rqKbcu1ummWVpLTDBNZHH7" target="_blank">
<img src="http://img.youtube.com/vi/SiGxu2N9ndU/mqdefault.jpg" alt="Watch the series" width="240" height="180" border="10" />
</a>

## How to install this example Golang project on Kubernetes

The easiest way to see how this works is to watch the series, but here's the short version.

1. clone this project
2. Set up a local kubernetes cluster - https://askcloudarchitech.com/posts/tutorials/local-kubernetes-startup-script/
3. Install helm - `brew install helm`
4. Setup your keys - see the video here - https://www.youtube.com/watch?v=k0L_yR30PqI&list=PLSvCAHoiHC_rqKbcu1ummWVpLTDBNZHH7&index=5
5. Install with Helm

## How to tweak this project for your own uses

Since this is an example project, I'd encourage you to clone and rename this project to use for your own puposes. It's a good starter boilerplate

## Find a bug?

If you found an issue or would like to submit an improvement to this project, please submit an issue using the issues tab above. If you would like to submit a PR with a fix, reference the issue you created!

## Known issues (Work in progress)

This tutorial is till ongoing. The automation of the helm chart repo has not been completed yet. This is coming soon!

## Like this project?

If you are feeling generous, buy me a coffee! - https://buymeacoffee.com/zeinab.ibrahim
