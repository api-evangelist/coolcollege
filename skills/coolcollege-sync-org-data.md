---
name: Sync organization data into Cool College
description: Push department, position, user, and learning-project enrollment data from a third-party HR/OA system into Cool College via the thirdoa event endpoints.
api: openapi/coolcollege-openapi-original.json
operations:
  - "POST /event/dept"
  - "POST /event/post"
  - "POST /event/user"
  - "POST /event/study_project_user"
---

# Sync organization data (third-party OA → Cool College)

Base URL: `https://openapi.coolcollege.cn/api/v2`. Authenticate first with the HMAC-signature exchange (see the authenticate-and-read-learning skill).

## Recommended order
Sync the org hierarchy before people so references resolve:

1. **Departments** — `POST /event/dept` with `部门信息推送` payload (`data[]` of `部门信息`: `id`, `name`, `parent_id`, `depart_order`, `is_delete`, timestamps) and `data_count`.
2. **Positions** — `POST /event/post` with `岗位信息推送` payload (`data[]` of `岗位信息`: `post_id`, `post_name`, `post_order`, `is_delete`, timestamps).
3. **Users** — `POST /event/user` with `人员信息推送` payload (`data[]` of `人员信息`: `jobnumber`, `mobile`, `email`, `department_ids`, `post_ids`, `is_admin`, `active`, `is_delete`, `extend_column`, timestamps).
4. **Learning-project enrollment** — `POST /event/study_project_user` with `第三方 学习项目报名学习信息` (`project_id`, `user_id`).

## Conventions
- Use `is_delete` to tombstone records rather than omitting them, and set `data_count` to the length of `data[]`.
- Responses use the `ApiResponse` envelope (`code`/`type`/`message`) — verify `code` before treating a sync as applied.
