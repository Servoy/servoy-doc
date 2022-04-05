---
layout: default
title: Migrating to NG Titanium
nav_order: 1
---

### **AngularJS to Angular.io What does it mean?**

With 2022 approaching, we just wanted to update you on the AngularJS sundown and what it means for you.

Servoy's **NG Client** suite is a deployment technology which delivers your applications to any browser, any desktop, iOS and Android from a single codebase and toolset. The NG Client front-end is built upon **AngularJS**, a powerful framework to enhance HTML5. 

In mid-2018, the AngularJS community [announced](https://docs.angularjs.org/misc/version-support-status) that it would enter a long-term-support (LTS) period, expiring **December 31, 2021**. AngularJS had quickly been succeeded by **Angular.io** (Angular), a newer, more powerful framework, loosely based on the original AngularJS.

### **Servoy’s NG2 Strategy**

Prior to the AngularJS EOS announcement, Servoy had been researching and developing the successor to the NG Client for quite some time.

In 2020, Servoy announced the successor to the NG Client would begin to slowly roll out during 2021, beginning in v2021.03 with the core of the client technology and common components, followed by incremental updates to component packages during each quarterly release. 

It has been a massive effort and the work is ongoing. The result is that Servoy’s customers can continue to depend on a vendor-supported, future-proof platform, despite the ever-changing technology landscape.

We are excited to share more about the roadmap in our upcoming New Year’s webinar (TBA).

#### **AngularJS Extended Support (XLTS)**

In addition to the investments made into future-proofing the platform, Servoy also secured a 3rd-party extended support contract for AngularJS, which includes a service level agreement and license for a version of AngularJS that is maintained by [XLTS.dev](https://xlts.dev/angularjs) (which includes founding contributors to the original framework.) This support includes addressing potential security weaknesses and fixing breakages caused by web browsers and jQuery.

For the Servoy community, it provides a solid bridge to extend critical support for the framework used by the NG Client. This affords Servoy enough time to help our customers manage the migration process, testing and hardening.

The XLTS support runs until November 1, 2022 (with an option to renew on an annual basis).

### **What does the transition mean for NG Client Applications ?**

#### **Vulnerabilities**

First of all, your NG Client users will experience no change in January and there are no vulnerabilities associated with this transition. Of course issues can arise at any time. Fortunately, ALL of Servoy’s customers are covered by the XLTS contract, who will provide patches for the following scenarios:

- A security flaw is detected in XLTS for AngularJS
- One of the major browsers releases a version that will cause current production applications using the XLTS for AngularJS to stop working
- The jQuery library releases a version that will cause current production applications using XLTS for AngularJS to stop working

#### **Compliance**

If your application is subject to 3rd-party security audits, then you may be concerned with compliance. It is possible that such an audit could flag AngularJS-based applications. Fortunately, the XLTS distribution is vendor-supported and you can have confidence that your application will pass security and compliance audits.

###  

All forthcoming releases of Servoy will be compliant. However, If you are concerned with compliance early in 2022, then please upgrade to the latest Servoy [**v2021.3.x LTS**](https://build.servoy.com/latest/servoy_lts_latest/) build. If you have additional questions about this topic, or feel you need assistance, please contact [info@servoy.com](mailto:info@servoy.com) or call us at +1 866 489 5147. 