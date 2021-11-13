# ElectricityMap - Variability Analysis

## Variability modeling

<!-- 1. Main Features

    - World Map
    - Zones, regions
    - Production/consumption information
    - Energy import/export calculation
    - CO2 and CO2eq emission calculations, carbon intensity
    - Historical data
    - Forecast data
    - Languages(?) -->

Modern interactive software almost always entails the ability for certain levels of configurability, where the software's performance changes based on user demands or actions. This is known as software *variability*, where the underlying software defines and supports the ability of the user to change its functionality at will. Variability can vary widely, from minor changes (such as a change in user language, e.g. English to Dutch) to major changes that redefine how the user interacts with the software itself, and is an important factor in the deployment of software that has a diverse userbase in mind. ElectricityMap is no exception. EM supports a number of minor and major variable features, from the ability of users to change their display language, to the comparison between energy production vs. consumption displayed on the map. However, because EM pursues a relatively singular purpose - providing users with the ability to measure and track energy production and consumption - the software's variability is limited by this constraint. 

The main ElectricityMap service provides an interactive map for users to navigate, where countries and regions around the world are interactive to the user. Users can isolate specific regions to learn more about electricity consumption and production and the impacts thereof, and such calculation of impact in CO2-equivalent terms (CO2eq) is variable itself. The calculation of the CO2eq remains the same, regardless of which region of the world you are in, but the data changes from region to region. Furthermore, users are able to switch between viewing production and consumption information on the map, which depends on two different map displays. Beyond these relatively basic features, however, there does not exist much variability because EM serves a somewhat static purpose: to be informative.

Corporations can pay EM for access to the closed source back-end API and redis cache. This can be done on a per country basis or for all regions at once. A good example of this is the Danish electric utility company Barry[^Barry], which uses EM's API to show their customers the carbon footprint of their electricity. Taking this perspective as an example, we can imagine that perhaps Barry (and other such clients) would desire a bit more variability (or flexibility), such as a more thorough breakdown of energy production and consumption for their customers, as compared to the one that is publicly available. Such a breakdown would likely enable Barry and other such clients to take more concrete steps to reduce their overall footprint. Another type of client are non-profits and think tanks, like Ember[^Ember]. Ember uses the EM API to shape public policy, support and empower campaign organisations, and change global narratives regarding CO2 emissions.

<!-- 2. Incompatibilities between features -->

### Feature Model

ElectricityMap depends on numerous APIs to deliver the vast majority of its services. These APIs are used to collect and deliver both historical and real-time data feeds to its customers, as well as to provide each regional parser with data for the front-end product. Each regional parser has a production and consumption map, as well as the relevant CO2eq impact calculations. The consumption map contains the added features of import and export flows from neighboring countries/regions. The historical data is not accessible publicly. It can only be viewed by choosing one of the available paid options on their site.

![](https://i.imgur.com/Ek9HRjx.png)


<!-- <p align="center" width="100%">
  <img width="60%" src="https://i.imgur.com/QE0bh7T.png" />
</p> -->

### Incompatibilities
As far as incompatibilities go, there aren't too many, given the nature and structure of EM. If we consider the features available to paying clients vs non-paying ones, yes there is a disparity. We believe this falls under software variability, since certain features of the application (such as the closed source API) are only accessible to those who are willing to pay for it. Considering that Tomorrow Co., is a registered for-profit that does need to be profitable to some extent, whether this kind of pay-walling is good or not is debatable.

## Variability management

In this section, we would like to discuss how the variability is managed for ElectricityMap’s different stakeholders. For ElectricityMap, its stakeholders can range from end-users who want to learn more about the energy consumption data, to companies who wish to use the energy data for better marketing strategies, to the enthusiast who is willing to contribute to the project both code-wise or data-wise, then to the developers who are heavily involved in the major features and updates of the project, who are mostly working in the Tomorrow team.

Certainly, for different stakeholders, ElectricityMap also offers them different levels of variability. For example, for the standard end-user who just wish to learn more about global energy production and consumption, they can directly view the ElecticityMap [website](https://www.electricitymap.org/map) for quick access to information just on their computer. For the variability in the hardware and the platforms. There are also end-user who prefer mobile, so the android user can find the app on [Google Play](https://play.google.com/store/apps/details?id=com.tmrow.electricitymap&utm_source=github), and the ios user can find the app on [App store](https://apps.apple.com/us/app/electricity-map/id1224594248%26utm_source%3Dgithub). For the stakeholders like companies who wish to use the ElectricityMap API, such as climate think tank Ember[^Ember], Danish electricity retailer Barry Energy[^Barry] which we discussed in the Variability Modelling section.[^ele_map_api] They have two possible pricing schemes, they can either pay 500 dollars per month to get the historical data, or single-country data. If the company wishes to get the multi-country data, they need to get in contact with Tomorrow team. For contributors as stakeholders, in this case, they will be joining the growing open-source contributors' community of ElectricityMap. They can rely on the information in the [Wiki](https://github.com/tmrowco/electricitymap-contrib/wiki) which is being maintained by the developers from the Tomorrow team. However, there is still variability with the technical knowledge of the contributors. In order to ease the variability management in the development phase, the Tomorrow team and the lead developers assigned different tasks to contributors with various levels of coding knoweledge. The contributors who are less familiar with the code, they will get instructed on how to contribute as a non-coder[^wiki_non_coder]. Unlike the developers who will be working on building new parsers, fixing old parsers, or making changes to the frontend. The non-coder contributors have the opportunity to find and verify data sources, or helping to add new translations.

In the next section, we will discuss how the variability is imlemented in ElectricityMap.
<!-- - issues : system testing, cannot rely on developers, need to have a process, how things automatically tested -->

## Variability Implementation
For now, we have seen many configurations that are needed in ElectricityMap, but how are these configurations been resolved. 

### Platform
For developers, the set-up is quite simple, the project is available on github and requires Python 3.6/3.7 development requirement, docker, and poetry, the set-up is very straightforward and easy to manage. As we have previously discussed, users of ElectrcityMap can view the project as a webpage or download it as an Android/IOS app. Therefore, ElectricityMap supports a wide variety of platforms. For users, this means that the project runs on any hardware with no extra complexity. For developers, this means that configurations of the project should be viewable on all platforms.

There are 2 folders configuation parameters on the platform variability: `web` and `mobileapp`. `web` folder focusses on the configuration parameters of the website and the setup of the mockserver. The configuration is divided into multiple files: `web/public/browserconfig.xml` configures the logo page of the website. `web/build.yaml` and corresponding `web/DOCKERFILE` install the dependencies and build the map.


### Parsers
Now let's take a look at the parsers. Usually, users are not concerned about how the data get parsed. Therefore the variability in parsers is mostly meant for developers. While the parsers do not necessarily need many configuration parameters, as they are mostly hardcoded, it is important to keep the output consistent. ElectricityMap does not have any pre-specified design pattern, but the output of each parser needs to adhere to some format, which is shown in .....: each zone parser requires a `fetch_production()` function.

Furthermore, some parsers need external packages to function. For example, real-time data of Maharashtra (India) is displayed as an image (at the source), therefore cv2 and OCR packages need to be used to extract relevant information. If a developer decides to edit or update new packages, they can be found at `pyproject.toml`, where the external dependencies and their versions are specified. If external API-key is needed, the key needs to be manually added to `secrets.env`, which is ignored by gitignore.

### Languages
Finally, ElectrictyMap is a visualisation of world map，users will interact with the interface very frequently, this interaction also causes variability in the system. A major variability in the system would be the language preference. Currently, ElectricityMap supports 22 languages. The users can select their favorite language in the UI. In the project, the translations are stored in `web／locales` folder, where each language has its JSON file(`ar.json, zh-cn.json` etc). Translators can modify these files without any major trouble. It is also possible to use `translation_helper.js`, which helps translators to find untranslated sentences. The language configuration file is found in `web/locales-config.json`, where each language is explained. 


## References

[^ele_map_api]: Electricity Map API https://api.electricitymap.org/

[^wiki_non_coder]: Wiki on how to contribute as a non-coder https://github.com/tmrowco/electricitymap-contrib/wiki/How-to-contribute-as-a-non-coder

[^Ember]: [Ember](https://ember-climate.org/) coal to clean energy

[^Barry]: [Barry Energy](https://barry.energy/dk) Danish electricity retailer