`This is the optimize version of the query`

The query can be optimized by _indexing_ columns used in the **WHERE** clause for filtering. 
Additionally, some of the join conditions can be simplified, for example, the type column 
in the _affiliates_ table can be included in the **LEFT JOIN** conditions, instead of having 
separate LEFT JOIN statements for each type.

### Column Indexing

```mysql
ALTER TABLE `affiliates` ADD INDEX `affiliates_idx_type` (`type`);
ALTER TABLE `jobs` ADD INDEX `jobs_idx_publish_status_id` (`publish_status`,`id`);
```

### Generate another alternative with Covering Indexes (larger, but can be faster)
```mysql
ALTER TABLE `affiliates` ADD INDEX `affiliates_idx_type` (`type`);
ALTER TABLE `basic_abilities` ADD INDEX `basic_abilities_idx_id_deleted_name` (`id`,`deleted`,`name`);
ALTER TABLE `jobs` ADD INDEX `jobs_idx_publish_status_id` (`publish_status`,`id`);
ALTER TABLE `jobs_basic_abilities` ADD INDEX `jobs_abilities_idx_job_id_basic_id` (`job_id`,`basic_ability_id`);
ALTER TABLE `jobs_career_paths` ADD INDEX `jobs_paths_idx_job_id_affiliate_id` (`job_id`,`affiliate_id`);
ALTER TABLE `jobs_personalities` ADD INDEX `jobs_personalities_idx_job_id_personality_id` (`job_id`,`personality_id`);
ALTER TABLE `jobs_practical_skills` ADD INDEX `jobs_skills_idx_job_id_practical_id` (`job_id`,`practical_skill_id`);
ALTER TABLE `jobs_rec_qualifications` ADD INDEX `jobs_qualification_idx_job_id_affiliate_id` (`job_id`,`affiliate_id`);
ALTER TABLE `jobs_req_qualifications` ADD INDEX `jobs_qualification_idx_job_id_affiliate_id` (`job_id`,`affiliate_id`);
ALTER TABLE `jobs_tools` ADD INDEX `jobs_tools_idx_job_id_affiliate_id` (`job_id`,`affiliate_id`);
ALTER TABLE `personalities` ADD INDEX `personalities_idx_id_deleted_name` (`id`,`deleted`,`name`);
ALTER TABLE `practical_skills` ADD INDEX `practical_skills_idx_id_deleted_name` (`id`,`deleted`,`name`);
```

### Optimized SQL Query

```mysql
SELECT Jobs.id AS `Jobs__id`,
    Jobs.name AS `Jobs__name`,
    Jobs.media_id AS `Jobs__media_id`,
    Jobs.job_category_id AS `Jobs__job_category_id`,
    Jobs.job_type_id AS `Jobs__job_type_id`,
    Jobs.description AS `Jobs__description`,
    Jobs.detail AS `Jobs__detail`,
    Jobs.business_skill AS `Jobs__business_skill`,
    Jobs.knowledge AS `Jobs__knowledge`,
    Jobs.location AS `Jobs__location`,
    Jobs.activity AS `Jobs__activity`,
    Jobs.academic_degree_doctor AS `Jobs__academic_degree_doctor`,
    Jobs.academic_degree_master AS `Jobs__academic_degree_master`,
    Jobs.academic_degree_professional AS `Jobs__academic_degree_professional`,
    Jobs.academic_degree_bachelor AS `Jobs__academic_degree_bachelor`,
    Jobs.salary_statistic_group AS `Jobs__salary_statistic_group`,
    Jobs.salary_range_first_year AS `Jobs__salary_range_first_year`,
    Jobs.salary_range_average AS `Jobs__salary_range_average`,
    Jobs.salary_range_remarks AS `Jobs__salary_range_remarks`,
    Jobs.restriction AS `Jobs__restriction`,
    Jobs.estimated_total_workers AS `Jobs__estimated_total_workers`,
    Jobs.remarks AS `Jobs__remarks`,
    Jobs.url AS `Jobs__url`,
    Jobs.seo_description AS `Jobs__seo_description`,
    Jobs.seo_keywords AS `Jobs__seo_keywords`,
    Jobs.sort_order AS `Jobs__sort_order`,
    Jobs.publish_status AS `Jobs__publish_status`,
    Jobs.version AS `Jobs__version`,
    Jobs.created_by AS `Jobs__created_by`,
    Jobs.created AS `Jobs__created`,
    Jobs.modified AS `Jobs__modified`,
    Jobs.deleted AS `Jobs__deleted`,
    JobCategories.id AS `JobCategories__id`,
    JobCategories.name AS `JobCategories__name`,
    JobCategories.sort_order AS `JobCategories__sort_order`,
    JobCategories.created_by AS `JobCategories__created_by`,
    JobCategories.created AS `JobCategories__created`,
    JobCategories.modified AS `JobCategories__modified`,
    JobCategories.deleted AS `JobCategories__deleted`,
    JobTypes.id AS `JobTypes__id`,
    JobTypes.name AS `JobTypes__name`,
    JobTypes.job_category_id AS `JobTypes__job_category_id`,
    JobTypes.sort_order AS `JobTypes__sort_order`,
    JobTypes.created_by AS `JobTypes__created_by`,
    JobTypes.created AS `JobTypes__created`,
    JobTypes.modified AS `JobTypes__modified`,
    JobTypes.deleted AS `JobTypes__deleted`
FROM jobs
LEFT JOIN job_categories JobCategories
    ON (JobCategories.id = Jobs.job_category_id
        AND JobCategories.deleted IS NULL)
LEFT JOIN job_types JobTypes
    ON (JobTypes.id = Jobs.job_type_id
        AND JobTypes.deleted IS NULL)
LEFT JOIN jobs_affiliates JobsAffiliates
    ON Jobs.id = JobsAffiliates.job_id
LEFT JOIN affiliates
    ON affiliates.id = JobsAffiliates.affiliate_id
    AND affiliates.deleted IS NULL
    AND (affiliates.type IN (1, 2, 3)
         OR affiliates.name LIKE '%キャビンアテンダント%')
LEFT JOIN jobs_personalities JobsPersonalities
    ON Jobs.id = JobsPersonalities.job_id
LEFT JOIN personalities Personalities
    ON Personalities.id = JobsPersonalities.personality_id
    AND Personalities.deleted IS NULL
WHERE (JobCategories.name LIKE '%キャビンアテンダント%'
       OR JobTypes.name LIKE '%キャビンアテンダント%'
       OR Jobs.name LIKE '%キャビンアテンダント%'
       OR Jobs.description LIKE '%キャビンアテンダント%'
       OR Jobs.detail LIKE '%キャビンアテンダント%'
       OR Jobs.business_skill LIKE '%キャビンアテンダント%'
       OR Jobs.knowledge LIKE '%キャビンアテンダント%'
       OR Jobs.location LIKE '%キャビンアテンダント%'
       OR Jobs.activity LIKE '%キャビンアテンダント%'
       OR Jobs.salary_statistic_group LIKE '%キャビンアテンダント%'
       OR Jobs.salary_range_remarks LIKE '%キャビンアテンダント%'
       OR Jobs.restriction LIKE '%キャビンアテンダント%'
       OR Jobs.remarks LIKE '%キャビンアテンダント%'
       OR Personalities.name LIKE '%キャビンアテンダント%')
    AND publish_status = 1
    AND Jobs.deleted IS NULL
GROUP BY Jobs.id
ORDER BY Jobs.sort_order DESC, Jobs.id DESC
LIMIT 50 OFFSET 0;

```
