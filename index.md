---
title: 온라인 호스팅 지침
permalink: index.html
layout: home
---

# <a name="content-directory-az-120-planning-and-administering-microsoft-azure-for-sap-workloads"></a>콘텐츠 디렉터리: AZ-120: Planning and Administering Microsoft Azure for SAP Workloads

필요한 랩 파일은 [여기에서 다운로드](https://github.com/MicrosoftLearning/AZ-120-Planning-and-Administering-Microsoft-Azure-for-SAP-Workloads /archive/master.zip)할 수 있습니다.

다음은 각 랩 연습 및 데모의 하이퍼링크입니다.

## <a name="labs"></a>랩

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/'" %}
| 모듈 | 랩 |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
