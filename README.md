# Tracksuit Task
**1. Estimating Required Exposures**

For each category, I estimate the number of respondents that must be exposed to its qualifier in order to achieve approximately 200 qualified completes:

*required exposures = ⌈200/incidence rate⌉*

This ensures that low-incidence categories receive more exposure, while high-incidence categories require fewer respondents to reach the same completion target.

**2. Estimating Expected Respondent Burden**

Each category contributes an expected per-respondent interview burden defined as:

*expected time = incidence rate x category length*

This represents the average time cost of including a category in a survey version, accounting for the fact that only a fraction of respondents will qualify for the category survey.

**3. Constructing Survey Versions (Greedy Packing)**

I construct survey versions using a greedy bin-packing heuristic. Categories are sequentially added to a survey version as long as the cumulative expected interview time remains <480 seconds. If a category does not fit, a new survey version is created.

This produces survey versions that satisfy the expected interview length constraint. While heuristic rather than optimal, this approach is computationally efficient and practical for survey planning before fieldwork begins.

**4. Simulation of Monthly Data Collection**

To validate the design, I simulate respondent-level data collection over the course of a month.

Each simulated respondent:

- Is assigned a demographic profile sampled to reflect national population proportions (e.g., sex and age distributions)
- Is assigned to a survey version using a heuristic weighting scheme reflecting relative survey load across versions
- Is shown all categories within that survey version (reflecting prespecified survey design)
- Has qualification for each category simulated probabilistically using the category incidence rate
- Accumulates interview time based on categories for which they qualify

The simulation continues until all categories approximately reach their required exposure targets.

**5. Validation Metrics**

The system is evaluated using the following metrics:

- Mean interview length (target: <480 seconds)
- Tail interview length distribution (e.g., 95th percentile, maximum)
- Number of qualified completes per category (target: ≥200)
- Total number of respondents required (cost metric)

Together, these metrics evaluate whether the precomputed survey design satisfies both operational constraints and statistical performance requirements.

**6. Real-World Considerations**

Although the task assumes that category qualifiers consume zero interview time, real-world survey platforms impose limits on respondent burden at the qualifier level. In practice, respondents cannot be shown an unlimited number of screening questions, as excessive qualifiers may increase fatigue and drop-off rates. The framework can therefore be extended to include constraints on the maximum number of qualifiers shown per respondent, or small per-qualifier time penalties, to better reflect operational survey conditions.

Demographic control, by contrast, is handled at the sampling layer rather than within the survey design itself. Panel providers recruit respondents to match national distributions across gender, age, and region, and survey versions are assigned independently of demographic characteristics. Because category qualifiers are shown solely based on survey version (and not on respondent demographics), the demographic distribution of exposures for each category matches the national population in expectation.

If demographic representativeness needed to be modelled explicitly, I would treat it as a sampling-layer process operating independently of the survey design itself. The core idea is that respondents enter the system according to a known demographic distribution (e.g., age groups, gender x age cells, or region), while survey versions are assigned independently of those characteristics. Under these conditions, the demographic distribution of exposures to each category mirrors the underlying sample distribution in expectation.

**The modelling approach would involve three steps:**

**1. Define demographic cells**
The population would be partitioned into meaningful demographic groups (e.g., age bands such as 18–24, 25–34, etc.). Each group would be assigned a probability representing its share of the target population. These probabilities could reflect either national census distributions or balanced quota targets.

**2. Sample respondents from this distribution**
Each simulated respondent would be assigned a demographic label by sampling from the specified probability distribution. This mirrors how panel providers recruit respondents to satisfy demographic quota requirements in practice.

**3. Assign survey versions independently of demographics**
Respondents would then be randomly assigned to survey versions according to the survey allocation policy. Because version assignment is independent of demographic characteristics, each category is exposed to a random subset of the broader respondent pool. As a result, the demographic composition of category exposures converges toward the underlying population distribution over time.

This approach keeps the survey design relatively simple, because demographic balancing happens during recruitment rather than through separate routing rules inside the survey system itself.
