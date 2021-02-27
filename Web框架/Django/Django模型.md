---
title: Django 模型
description: 
published: true
date: 2021-02-27T08:51:28.974Z
tags: django
editor: markdown
dateCreated: 2021-02-27T07:41:11.095Z
---

# Django 中的 ORM

传统的数据库操作都是通过编写 SQL 语句，且针对不同的数据库还存在一定的差异。 

而 ORM 则是通过使用类和对象对数据库进行操作，避免了直接使用 SQL 语句，从而简化对数据库的操作。这种程序技术的底层主要是通过映射机制实现的。

如图是 ORM 与数据库的映射关系图。

```diagram
PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiB2ZXJzaW9uPSIxLjEiIHdpZHRoPSIzODUiIGhlaWdodD0iMjI0IiB2aWV3Qm94PSItMC41IC0wLjUgMzg1IDIyNCIgY29udGVudD0iJmx0O214ZmlsZSBob3N0PSZxdW90O2VtYmVkLmRpYWdyYW1zLm5ldCZxdW90OyBtb2RpZmllZD0mcXVvdDsyMDIxLTAyLTI3VDA4OjAyOjM3Ljg3M1omcXVvdDsgYWdlbnQ9JnF1b3Q7NS4wIChYMTE7IExpbnV4IHg4Nl82NCkgQXBwbGVXZWJLaXQvNTM3LjM2IChLSFRNTCwgbGlrZSBHZWNrbykgQ2hyb21lLzg4LjAuNDMyNC4xODIgU2FmYXJpLzUzNy4zNiZxdW90OyB2ZXJzaW9uPSZxdW90OzE0LjQuMiZxdW90OyBldGFnPSZxdW90O3ExMUtVaWQ5X1FRN1pMZDZLdktMJnF1b3Q7IHR5cGU9JnF1b3Q7ZW1iZWQmcXVvdDsmZ3Q7Jmx0O2RpYWdyYW0gaWQ9JnF1b3Q7RFlFbWladksyTHZmNGhUWWhsdXomcXVvdDsgbmFtZT0mcXVvdDtQYWdlLTEmcXVvdDsmZ3Q7NVpqTGNwc3dGSWFmUnR1T1FPYTJCSXpiUlRPZGFSWk5sd3FTUWEyTVBFS083VDU5SlNNQ0dOekp4STR6Y1RjZXpxK0x4ZmNmbndNR0tGM3RQa3U4THU4RW9SeTRrT3dBbWdQWERWeW9QNDJ3YndUUGR4dWhrSXcwRXV5RWUvYUhOcUxUcWh0R2FHMjFSbEpDY01YV1F6RVhWVVZ6TmRDd2xHSTduTFlVbkF5RU5TN280QmhHdU04eHA2TnBQeGhSWmFPR2J0RHBYeWdyeXZhYkhUOXFSbGE0bld3M3JrdE14TGEzSzhvQVNxVVFxcmxhN1ZMS0Ric2hsOFdKMGVlRFNWcXBseXl3M0o4dzM5aDdzK2RTKy9abXBkaFVoSnI1RGtESnRtU0szcTl4YmthMzJsMnRsV3JGN2ZDU2NaNEtMdVJoTFNLWWhzdGM2N1dTNGpmdGpmaDVTQitYZXNRZWdFcEZkeWR2b2tPalU0cUtGVlZ5cjZmWUJaSGZyTERaNUxRN2JIdmUyQ2xsenhZOTBhYUVUWWZpZWVlT21MNncwS1lCb2hIQWI5L3ZSZ3oxdmFraHFDR1FTbFQwaUo2Vk1HZEZwY05jczZCYVR3d3BwbE14dGdNclJvajVta2xuT3UrZzJWNVV5djZZWEtlTjdTR2RDL2pnT05IUUNJUkdSc3pnaEJId2ZCOW00MFRPQXBBNElFbk96T2dPbWpNYlp6aUVhZXI3cHd5OUJGUVlmdktHV0lOZ2hOVjN4bGlSZno1V2J3S3JCK0lGU0NLUWhZWnY3TndhWDExUXJzYlhuK1Nyc1VZWnlId1FRaEFITjhjM2hGZmpHN3h0ZjFzdXFaOVA5amNTUkk4UVhnWWhDdEg3TmJod1JIQStycWovUjMrYndhTUhqU3YydDJpaVVJU20rTWJobTlhSHhTS0VoengrcS9xQUl1OGQrMXY3UmpIbUdxYTN4dldhZmMxeEpyanFCNGM1aUFMVDJPSU1KTjdOQWI1aVkydExlNDhlSmZvbDFZWkNxbElVb3NJODY5U2pjdG5OK1NyRTJsTDlSWlhhVzZ4NG84U1FPZDB4OWRDNy9tbTIwaFNhYUw2ek94K0NmUnRVK3RZZStrRnZsUW03Wlllb1hYZnNiNjJ3VkxGNWV6ZnRndU82Wm5rckx4aHZqMGh3WFQ2blVNUElnUG0zeVpxajJNamN6ckx2RW5yamdxcGUrUjJuZ3FRY0svWTAzUDBzVzlGSHNYWEErWVhlSEZ2Nk9udThzVDFOR2IrR1A3T1A0czlyZjNidjU2cy80ZXVKRW55dXJ6cnMvbU03alBYK3FFVFpYdz09Jmx0Oy9kaWFncmFtJmd0OyZsdDsvbXhmaWxlJmd0OyI+PGRlZnM+PGZpbHRlciBpZD0iZHJvcFNoYWRvdyI+PGZlR2F1c3NpYW5CbHVyIGluPSJTb3VyY2VBbHBoYSIgc3RkRGV2aWF0aW9uPSIxLjciIHJlc3VsdD0iYmx1ciIvPjxmZU9mZnNldCBpbj0iYmx1ciIgZHg9IjMiIGR5PSIzIiByZXN1bHQ9Im9mZnNldEJsdXIiLz48ZmVGbG9vZCBmbG9vZC1jb2xvcj0iIzNENDU3NCIgZmxvb2Qtb3BhY2l0eT0iMC40IiByZXN1bHQ9Im9mZnNldENvbG9yIi8+PGZlQ29tcG9zaXRlIGluPSJvZmZzZXRDb2xvciIgaW4yPSJvZmZzZXRCbHVyIiBvcGVyYXRvcj0iaW4iIHJlc3VsdD0ib2Zmc2V0Qmx1ciIvPjxmZUJsZW5kIGluPSJTb3VyY2VHcmFwaGljIiBpbjI9Im9mZnNldEJsdXIiLz48L2ZpbHRlcj48L2RlZnM+PGcgZmlsdGVyPSJ1cmwoI2Ryb3BTaGFkb3cpIj48cmVjdCB4PSIwIiB5PSIwIiB3aWR0aD0iODYiIGhlaWdodD0iMjEyIiByeD0iMTIuOSIgcnk9IjEyLjkiIGZpbGw9IiNkYWU4ZmMiIHN0cm9rZT0iIzZjOGViZiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxyZWN0IHg9IjIzIiB5PSIxMiIgd2lkdGg9IjQwIiBoZWlnaHQ9IjIwIiBmaWxsPSJub25lIiBzdHJva2U9Im5vbmUiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogMzhweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAyMnB4OyBtYXJnaW4tbGVmdDogMjRweDsiPjxkaXYgc3R5bGU9ImJveC1zaXppbmc6IGJvcmRlci1ib3g7IGZvbnQtc2l6ZTogMDsgdGV4dC1hbGlnbjogY2VudGVyOyAiPjxkaXYgc3R5bGU9ImRpc3BsYXk6IGlubGluZS1ibG9jazsgZm9udC1zaXplOiAyMXB4OyBmb250LWZhbWlseTogSGVsdmV0aWNhOyBjb2xvcjogIzAwMDAwMDsgbGluZS1oZWlnaHQ6IDEuMjsgcG9pbnRlci1ldmVudHM6IGFsbDsgZm9udC13ZWlnaHQ6IGJvbGQ7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPk9STTwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSI0MyIgeT0iMjgiIGZpbGw9IiMwMDAwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMjFweCIgdGV4dC1hbmNob3I9Im1pZGRsZSIgZm9udC13ZWlnaHQ9ImJvbGQiPk9STTwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgeD0iMTIuNSIgeT0iNTYiIHdpZHRoPSI2MSIgaGVpZ2h0PSIzNiIgcng9IjUuNCIgcnk9IjUuNCIgZmlsbD0iIzAwY2M2NiIgc3Ryb2tlPSJub25lIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDU5cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogNzRweDsgbWFyZ2luLWxlZnQ6IDE0cHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTRweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPuexuzwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSI0MyIgeT0iNzgiIGZpbGw9IiMwMDAwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTRweCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+57G7PC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCB4PSIxMi41IiB5PSIxMDgiIHdpZHRoPSI2MSIgaGVpZ2h0PSIzNiIgcng9IjUuNCIgcnk9IjUuNCIgZmlsbD0iIzAwY2M2NiIgc3Ryb2tlPSJub25lIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDU5cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogMTI2cHg7IG1hcmdpbi1sZWZ0OiAxNHB4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDE0cHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj7lr7nosaE8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iNDMiIHk9IjEzMCIgZmlsbD0iIzAwMDAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxNHB4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj7lr7nosaE8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IHg9IjEyLjUiIHk9IjE1OSIgd2lkdGg9IjYxIiBoZWlnaHQ9IjM2IiByeD0iNS40IiByeT0iNS40IiBmaWxsPSIjMDBjYzY2IiBzdHJva2U9Im5vbmUiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogNTlweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAxNzdweDsgbWFyZ2luLWxlZnQ6IDE0cHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTRweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPuWxnuaApzwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSI0MyIgeT0iMTgxIiBmaWxsPSIjMDAwMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE0cHgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPuWxnuaApzwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgeD0iMjg3IiB5PSIwIiB3aWR0aD0iODYiIGhlaWdodD0iMjEyIiByeD0iMTIuOSIgcnk9IjEyLjkiIGZpbGw9IiNmZmU2Y2MiIHN0cm9rZT0iI2Q3OWIwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxyZWN0IHg9IjMxMCIgeT0iMTIiIHdpZHRoPSI0MCIgaGVpZ2h0PSIyMCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSJub25lIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDM4cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogMjJweDsgbWFyZ2luLWxlZnQ6IDMxMXB4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDIxcHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyBmb250LXdlaWdodDogYm9sZDsgd2hpdGUtc3BhY2U6IG5vcm1hbDsgd29yZC13cmFwOiBub3JtYWw7ICI+REI8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iMzMwIiB5PSIyOCIgZmlsbD0iIzAwMDAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIyMXB4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIiBmb250LXdlaWdodD0iYm9sZCI+REI8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IHg9IjI5OS41IiB5PSI1NiIgd2lkdGg9IjYxIiBoZWlnaHQ9IjM2IiByeD0iNS40IiByeT0iNS40IiBmaWxsPSIjZmY4MDAwIiBzdHJva2U9Im5vbmUiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogNTlweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiA3NHB4OyBtYXJnaW4tbGVmdDogMzAxcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTRweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPuihqDwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSIzMzAiIHk9Ijc4IiBmaWxsPSIjMDAwMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE0cHgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPuihqDwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgeD0iMjk5LjUiIHk9IjEwOCIgd2lkdGg9IjYxIiBoZWlnaHQ9IjM2IiByeD0iNS40IiByeT0iNS40IiBmaWxsPSIjZmY4MDAwIiBzdHJva2U9Im5vbmUiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogNTlweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAxMjZweDsgbWFyZ2luLWxlZnQ6IDMwMXB4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDE0cHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj7ooYw8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iMzMwIiB5PSIxMzAiIGZpbGw9IiMwMDAwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTRweCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+6KGMPC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCB4PSIyOTkuNSIgeT0iMTU5IiB3aWR0aD0iNjEiIGhlaWdodD0iMzYiIHJ4PSI1LjQiIHJ5PSI1LjQiIGZpbGw9IiNmZjgwMDAiIHN0cm9rZT0ibm9uZSIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2g+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OiB2aXNpYmxlOyB0ZXh0LWFsaWduOiBsZWZ0OyIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6IGZsZXg7IGFsaWduLWl0ZW1zOiB1bnNhZmUgY2VudGVyOyBqdXN0aWZ5LWNvbnRlbnQ6IHVuc2FmZSBjZW50ZXI7IHdpZHRoOiA1OXB4OyBoZWlnaHQ6IDFweDsgcGFkZGluZy10b3A6IDE3N3B4OyBtYXJnaW4tbGVmdDogMzAxcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTRweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPuWtl+autTwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSIzMzAiIHk9IjE4MSIgZmlsbD0iIzAwMDAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxNHB4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj7lrZfmrrU8L3RleHQ+PC9zd2l0Y2g+PC9nPjxwYXRoIGQ9Ik0gNzkuODcgNzQgTCAyOTMuMTMgNzQiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBzdHJva2UtZGFzaGFycmF5PSIzIDMiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBkPSJNIDc0LjYyIDc0IEwgODEuNjIgNzAuNSBMIDc5Ljg3IDc0IEwgODEuNjIgNzcuNSBaIiBmaWxsPSIjMDAwMDAwIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGQ9Ik0gMjk4LjM4IDc0IEwgMjkxLjM4IDc3LjUgTCAyOTMuMTMgNzQgTCAyOTEuMzggNzAuNSBaIiBmaWxsPSIjMDAwMDAwIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGQ9Ik0gNzkuODcgMTI2IEwgMjkzLjEzIDEyNiIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHN0cm9rZS1kYXNoYXJyYXk9IjMgMyIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGQ9Ik0gNzQuNjIgMTI2IEwgODEuNjIgMTIyLjUgTCA3OS44NyAxMjYgTCA4MS42MiAxMjkuNSBaIiBmaWxsPSIjMDAwMDAwIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGQ9Ik0gMjk4LjM4IDEyNiBMIDI5MS4zOCAxMjkuNSBMIDI5My4xMyAxMjYgTCAyOTEuMzggMTIyLjUgWiIgZmlsbD0iIzAwMDAwMCIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBkPSJNIDc5Ljg3IDE3NyBMIDI5My4xMyAxNzciIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBzdHJva2UtZGFzaGFycmF5PSIzIDMiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBkPSJNIDc0LjYyIDE3NyBMIDgxLjYyIDE3My41IEwgNzkuODcgMTc3IEwgODEuNjIgMTgwLjUgWiIgZmlsbD0iIzAwMDAwMCIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBkPSJNIDI5OC4zOCAxNzcgTCAyOTEuMzggMTgwLjUgTCAyOTMuMTMgMTc3IEwgMjkxLjM4IDE3My41IFoiIGZpbGw9IiMwMDAwMDAiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PC9nPjxzd2l0Y2g+PGcgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ii8+PGEgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoMCwtNSkiIHhsaW5rOmhyZWY9Imh0dHBzOi8vd3d3LmRpYWdyYW1zLm5ldC9kb2MvZmFxL3N2Zy1leHBvcnQtdGV4dC1wcm9ibGVtcyIgdGFyZ2V0PSJfYmxhbmsiPjx0ZXh0IHRleHQtYW5jaG9yPSJtaWRkbGUiIGZvbnQtc2l6ZT0iMTBweCIgeD0iNTAlIiB5PSIxMDAlIj5WaWV3ZXIgZG9lcyBub3Qgc3VwcG9ydCBmdWxsIFNWRyAxLjE8L3RleHQ+PC9hPjwvc3dpdGNoPjwvc3ZnPg==
```

Django 中的模型都是一个 Python 类，这些类继承自 `django.db.models.Model`，ORM 把该类映射成数据库中的表。类的实例对象映射成数据库中的数据行（或者是一条记录），把类的属性映射成表中的字段（或每一列）

例如下面的模型：

```python
from django.db import models

class Person(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()
```

采用 MySQL 8.0 数据库引擎，其对应的创建表的 SQL 语句如下：

```sql
CREATE TABLE `app02_person` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(200) NOT NULL,
  `email` varchar(254) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

表结构如下：

```sql
mysql> desc app02_person;
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int          | NO   | PRI | NULL    | auto_increment |
| name  | varchar(200) | NO   |     | NULL    |                |
| email | varchar(254) | NO   |     | NULL    |                |
+-------+--------------+------+-----+---------+----------------+
3 rows in set (0.01 sec)
```

# 字段类型

> [字段类型参考文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#model-field-types)
{.is-success}

针对数据库中的字段类型，Django ORM 模型中每一个字段都是某个 Field 类的实例。见如下表格。

| 字段类型                                       | 数据库类型              | 描述                                                         |
| ---------------------------------------------- | ----------------------- | ------------------------------------------------------------ |
| `AutoField`                                    | `int auto_increment`    | 默认自增主键 ID                                              |
| `BigAutoField`                                 | `bigint auto_increment` | 64位整数自增 ID                                              |
| `BigIntegerField`                              | `bigint`                | 64 位的整数                                                  |
| `BinaryField`                                  | `longblob`              | 存储原始二进制数据                                           |
| `BooleanField`                                 | `tinyint(1)`            | true / false 字段                                            |
| `CharField(max_lengh=20)`                      | `varchar(20)`           | 字符串字段。`Max_length=20`，明确字符长度                    |
| `DateField`                                    | `date`                  | 日期， `datetime.date` 实例表示<br>`auto_now=True`，数据被更新就会更新时间 <br/>`auto_now_add=True`，数据第一次产生时。 |
| `DateTimeField`                                | `datetime(6)`           | 日期和时间`datetime.datetime` 实例表示                       |
| `DecimalField(max_digits=5, decimal_places=2)` | `decimal(5, 2)`         | `max_digits=5`，限定数字的最大位数(包含小数位)<br/>`decimal_places=2`，限制小数的最大位数。 |
| `DurationField`                                | `bigint`                | 存储时间段的字段                                             |
| `EmailField`                                   | `varchar(254)`          | 用于校验电子邮件地址                                         |
| `FileField`                                    | `varchar(100)`          | `upload_to='uploads/'`，文件将被上传到`MEDIA_ROOT/uploads`<br/>`uploads/%Y/%m/%d/`，文件被保存到`MEDIA_ROOT/uploads/2015/01/30` |
| `FilePathField`                                | `varchar(100)`          | `path=images_path`，可以使绝对路径，也可以是一个可调用对象。[更多用法](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#filepathfield) |
| `FloatField`                                   | `double`                | 在 Python 中用一个 `float` 实例表示的浮点数。                |
| `ImageField`                                   | `varchar(100)`          | `upload_to`，上传存储位置<br/>`height_field`，每次保存模型实例时将自动填充图像的高度。<br/>`width_field`，每次保存模型实例时将自动填充图像的宽度<br/>需要依赖 [Pillow](https://pillow.readthedocs.io/en/latest/) 库。 |
| `IntegerField`                                 | `int`                   | 一个整数。从 `-2147483648` 到 `2147483647` 的值              |
| `GenericIPAddressField`                        | `char(39)`              | IPv4 或 IPv6 地址<br>`protocol`限制地址格式，`both`、`IPv4` 、 `IPv6`。匹配是不分大小写的。 |
| `JSONField`                                    | `json`                  | 存储 JSON 编码数据的字段，Django 3.1，[具体用法](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#jsonfield) |
| `SlugField`                                    | `tinyint(1)`            | 简短的标签，只包含字母、数字、下划线或连字符                 |
| `SmallIntegerField`                            | `smallint `             | 小整型，从 `-32768` 到 `32767` 的值                          |
| `TextField`                                    | `longtext`              | 大的文本字段                                                 |
| `TimeField`                                    | `time(6)`               | 时间，在 Python 中用 `datetime.time` 实例表示                |
| `URLField`                                     | `varchar(200)`          | URL                                                          |
| `UUIDField`                                    | `char(32)`              | 唯一标识符，使用 Python 的 [`UUID`](https://docs.python.org/3/library/uuid.html#uuid.UUID) 类 |

## 字段选项

> [字段选项参考文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#common-model-field-options)
{.is-success}

| 字段选项          | 描述                                                         |
 | ----------------- | ------------------------------------------------------------ |
 | `null`            | Django 将在数据库中存储空值为 `NULL`。默认为 `False`。对于　CharField 和 TextField　默认为空字符串 |
 | `blank`           | `blank=True`，表单验证将允许输入一个空值。默认为 `False` 。  |
 | `choices`         | 二元组，第一个值会储存在数据库中，而第二个值将只会用于在表单中显示。使用 `get_FOO_display()` 方法可以获取第二个值                               |
 | `db_column`       | 要使用的数据库列名。如果没有给出列名，Django 将使用字段名。  |
 | `db_index`        | 为该字段创建数据库索引。                                     |
 | `db_tablespace`   | 如果这个字段有索引，那么要为这个字段的索引使用的 [数据库表空间](https://docs.djangoproject.com/zh-hans/3.1/topics/db/tablespaces/) 的名称。默认是项目的 [`DEFAULT_INDEX_TABLESPACE`](https://docs.djangoproject.com/zh-hans/3.1/ref/settings/#std:setting-DEFAULT_INDEX_TABLESPACE) 设置（如果有设置），或者是模型的 [`db_tablespace`](https://docs.djangoproject.com/zh-hans/3.1/ref/models/options/#django.db.models.Options.db_tablespace) （如果有）。如果后端不支持索引的表空间，则忽略此选项。 |
 | `default`         | 默认值。可以是一个值或者是个可调用的对象。如果是个可调用对象，每次实例化模型时都会调用该对象。 |
 | `editable`        | 如果是 `False`，该字段将不会在管理或任何其他 [`ModelForm`](https://docs.djangoproject.com/zh-hans/3.1/topics/forms/modelforms/#django.forms.ModelForm) 中显示。在 [模型验证](https://docs.djangoproject.com/zh-hans/3.1/ref/models/instances/#validating-objects) 中也会跳过。默认为 `True`。 |
 | `error_messages`  | 覆盖该字段引发的默认消息。传入一个与你想覆盖的错误信息相匹配的键值的字典。 |
 | `help_text`       | 额外的“帮助”文本，随表单控件一同显示。即便你的字段未用于表单，它对于生成文档也是很有用的。 |
 | `primary_key`     | 如果设置为 `True` ，将该字段设置为该模型的主键。则 Django 不会自动在表（模型）中添加 id 列。每个模型都需要拥有一个设置了 `primary_key=True` 的字段（无论是显式的设置还是 Django 自动设置）。             |
 | `unique`          | 如果设置为 `True`，这个字段必须在整个表中保持值唯一。        |
 | `unique_for_date` | 将其设置为 [`DateField`](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#django.db.models.DateField) 或 [`DateTimeField`](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#django.db.models.DateTimeField) 的名称，要求该字段的日期字段值是唯一的。 |
 | `verbose_name`    | 字段的一个人类可读名称，如果没有给定详细名称，Django 会使用字段的属性名自动创建，并将下划线转换为空格 |
 | `validators`      | 要为该字段运行的验证器列表。                                 |


