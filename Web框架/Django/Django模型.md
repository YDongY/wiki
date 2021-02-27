---
title: Django 模型
description: 
published: true
date: 2021-02-27T09:55:48.500Z
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

# 字段选项

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

# 查询返回 QuerySet 的方法

| 方法                  | 描述                                                         |
| :-------------------- | :----------------------------------------------------------- |
| `filter()`            | 根据查询条件，返回满足条件参数的 QuerySet。多个参数通过底层 SQL 语句中的 AND 连接。 |
| `exclude()`           | 根据查询条件，返回不满足条件参数的 QuerySet。多个参数通过底层 SQL 语句中的 AND 连接，整个过程用 `NOT()` 括起来。 |
| `annotate()`          | 用所提供的[查询表达式](https://docs.djangoproject.com/zh-hans/3.1/ref/models/expressions/)列表对 QuerySet 中的每个对象进行注解 |
| `order_by()`          | 对查询结果排序，默认情况下，返回的结果是按照模型 Meta 中的 ordering 选项给出的排序元组排序的 |
| `reverse()`           | 反转 QuerySet 的默认顺序                                     |
| `distinct()`          | 执行 SQL`SELECT DISTINCT`查询以消除重复的行                  |
| `values()`            | 返回字典而不是模型实例                                       |
| `values_list()`       | 返回元组而不是模型实例                                       |
| `dates()`             | 返回一个 QuerySet，其中包含指定日期范围内的所有可用日期      |
| `datetimes()`         | 返回一个 QuerySet，其中包含指定日期和时间范围内的所有可用日期 |
| `none()`              | 创建一个空的 QuerySet                                        |
| `all()`               | 返回当前 QuerySet 的副本                                     |
| `union()`             | 使用 SQL `UNION`运算符组合两个或多个 QuerySet                |
| `intersection()`      | 使用 SQL `INTERSECT`运算符返回两个或多个 QuerySet 的共享元素 |
| `difference()`        | 使用 SQL `EXCEPT`运算子可传回第一个 QuerySet 中其他元素以外的元素 |
| `select_related()`    | 执行查询时选择所有相关数据（多对多关系除外）                 |
| `prefetch_related()`  | 执行查询时选择所有相关数据（包括多对多关系）                 |
| `defer()`             | 不要从数据库中检索命名字段。用于提高复杂数据集的查询性能     |
| `only()`              | 与—相反，`defer()`仅返回命名字段                             |
| `using()`             | 选择将使用哪个数据库查询集（使用多个数据库时）               |
| `select_for_update()` | 返回一个 QuerySet 并锁定表行，直到事务结束                   |
| `raw()`               | 执行原始 SQL 语句                                            |
| `AND (&)`             | 将两个 QuerySet 与 SQL `AND`运算符结合在一起。使用`AND (&)`在功能上等同于使用`filter()`多个参数 |
| `OR (|)`              | 将两个 QuerySet 与 SQL `OR` 运算符组合                       |

## exclude()

`exclude()` 将返回与给定的查找参数不匹配的对象的QuerySet，例如：

- 多个条件

```python
>>> BookInfo.objects.exclude(name='天龙八部',id=2)
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 笑傲江湖>, <BookInfo: 雪山飞狐>]>
```

用 SQL 术语：

```sql
SELECT ... WHERE NOT (name='天龙八部' AND id=2);
```

- 多次 exclude

```python
>>> BookInfo.objects.exclude(name='天龙八部').exclude(id=3)
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 雪山飞狐>]>
```

用 SQL 术语：

```sql
SELECT ... WHERE NOT name='天龙八部' AND NOT id=3;
```

## order_by() 和reverse()

- `order_by()` 对结果排序

```python
>>> BookInfo.objects.order_by() # 默认按字段升序排序
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 天龙八部>, <BookInfo: 笑傲江湖>, <BookInfo: 雪山飞狐>]>
>>> BookInfo.objects.order_by('-id') # id 倒叙排序
<QuerySet [<BookInfo: 雪山飞狐>, <BookInfo: 笑傲江湖>, <BookInfo: 天龙八部>, <BookInfo: 射雕英雄传>]>
>>> BookInfo.objects.order_by('?') # 随机排序
<QuerySet [<BookInfo: 雪山飞狐>, <BookInfo: 天龙八部>, <BookInfo: 射雕英雄传>, <BookInfo: 笑傲江湖>]>
```

- `reverse()` 反转QuerySet的默认顺序：

```python
>>> BookInfo.objects.reverse()
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 天龙八部>, <BookInfo: 雪山飞狐>, <BookInfo: 笑傲江湖>]>
>>> BookInfo.objects.reverse().reverse()
<QuerySet [<BookInfo: 笑傲江湖>, <BookInfo: 雪山飞狐>, <BookInfo: 天龙八部>, <BookInfo: 射雕英雄传>]>
```

模型必须具有默认顺序（通过设置 `models Meta`类的 `ordering` 选项）`reverse()` 才能有用。如果模型是无序的，则返回的 QuerySet 的排序顺序将毫无意义。

## values() 和 values_list()

- `values()` : 返回字典，每一个字典都代表一个对象，键与模型对象的属性名相对应。

```python
>>> BookInfo.objects.values()
<QuerySet [{'id': 3, 'name': '笑傲江湖', 'pub_date': datetime.date(1995, 12, 24), 'readcount': 20, 'commentcount': 80, 'is_delete': F}, {'id': 4, 'name': '雪山飞狐', 'pub_date': datetime.date(1987, 11, 11), 'readcount': 58, 'commentcount': 34, 'is_delete': False}, {: 2, 'name': '天龙八部', 'pub_date': datetime.date(1986, 7, 24), 'readcount': 36, 'commentcount': 40, 'is_delete': False}, {'id': 1, e': '射雕英雄传', 'pub_date': datetime.date(1980, 5, 1), 'readcount': 12, 'commentcount': 34, 'is_delete': False}]>
>>>
>>> BookInfo.objects.values('id','name') # 限制 id name
<QuerySet [{'id': 3, 'name': '笑傲江湖'}, {'id': 4, 'name': '雪山飞狐'}, {'id': 2, 'name': '天龙八部'}, {'id': 1, 'name': '射雕英雄传'}]>
>>>
```

- `values_list()`：与相同values()，返回元组：

```python
>>> BookInfo.objects.values_list('id','name')
<QuerySet [(3, '笑傲江湖'), (4, '雪山飞狐'), (2, '天龙八部'), (1, '射雕英雄传')]>
>>>
>>> BookInfo.objects.values_list('id')
<QuerySet [(3,), (4,), (2,), (1,)]>
>>> BookInfo.objects.values_list('id',flat=True) # flat=True 返回单个元素
<QuerySet [3, 4, 2, 1]>
>>>
>>> BookInfo.objects.values_list('name',flat=True)
<QuerySet ['笑傲江湖', '雪山飞狐', '天龙八部', '射雕英雄传']>
>>> BookInfo.objects.values_list('name',flat=True).get(id=2)
'天龙八部'
```

## date() 和datetimes()

使用 `dates()` 和 `datetimes()` 方法从数据库中返回有时间限制的记录。对于dates() 这些时间界限是 year，month，week 和 day。datetimes() 增加了hour，minute 和 second 界限。一些例子：

```python
>>> BookInfo.objects.filter(id__lt=3).dates('pub_date','year')  # 年
<QuerySet [datetime.date(1980, 1, 1), datetime.date(1986, 1, 1)]>
>>> BookInfo.objects.filter(id__lt=3).dates('pub_date','month') # 年-月
<QuerySet [datetime.date(1980, 5, 1), datetime.date(1986, 7, 1)]>
>>> BookInfo.objects.filter(id__lt=3).dates('pub_date','week')
<QuerySet [datetime.date(1980, 4, 28), datetime.date(1986, 7, 21)]>
>>> BookInfo.objects.filter(id__lt=3).dates('pub_date','day')   # 年-月-日
<QuerySet [datetime.date(1980, 5, 1), datetime.date(1986, 7, 24)]>
>>> BookInfo.objects.filter(id__lt=3).dates('pub_date','day',order='DESC') # 按日倒叙
<QuerySet [datetime.date(1986, 7, 24), datetime.date(1980, 5, 1)]>
```

## union() 和 intersection() 和 difference()

- `UNION()`

使用 SQL 的 UNION 操作符来组合两个或多个 QuerySet 的结果，即求并集。默认去重。要允许重复的值，使用 `all=True` 参数。

```python
>>> from app01.models import Person
>>> Person.objects.all()
<QuerySet [<Person: Jack>, <Person: Tom>, <Person: Mike>, <Person: Herry>, <Person: Jerry>, <Person: Lisa>, <Person: Frank>]>

>>> p1 = Person.objects.filter(id__gte=4)
>>> p1
<QuerySet [<Person: Herry>, <Person: Jerry>, <Person: Lisa>, <Person: Frank>]>

>>> p2 = Person.objects.filter(id__lte=5)
>>> p2
<QuerySet [<Person: Jack>, <Person: Tom>, <Person: Mike>, <Person: Herry>, <Person: Jerry>]>
```

```python
>>> q = p1.union(p2) # p1 和 p2 并集，不包括重复的
>>> q
<QuerySet [<Person: Herry>, <Person: Jerry>, <Person: Lisa>, <Person: Frank>, <Person: Jack>, <Person: Tom>, <Person: Mike>]>

>>> q = p1.union(p2, all = True) # # p1 和 p2 并集，包括重复的
>>> q
<QuerySet [<Person: Herry>, <Person: Jerry>, <Person: Lisa>, <Person: Frank>, <Person: Jack>, <Person: Tom>, <Person: Mike>, <Person: Herry>, <Person: Jerry>]>

>>> p3 = Person.objects.filter(id=1)
>>> p3
<QuerySet [<Person: Jack>]>
>>> q = p1.union(p2, p3) # 多个集合求并集
>>> q
<QuerySet [<Person: Herry>, <Person: Jerry>, <Person: Lisa>, <Person: Frank>, <Person: Jack>, <Person: Tom>, <Person: Mike>]>
```

- `intersection()`

使用 SQL 的 INTERSECT 操作符来返回两个或多个 QuerySet 的共享元素，即求交集

- `difference()`

使用 SQL 的 EXCEPT 操作符，只保留存在于 QuerySet 中的元素，而不保留在其他 QuerySet 中的元素，即求差集

## select_related() 和 prefetch_related()

- `select_related()`：返回一个 QuerySet 并且查询出外键相关联的数据，意味着首次查询比较耗时，但是以后使用外键关系将不需要数据库查询。

例如

```python
# 1. 访问数据库
person = PeopleInfo.objects.get(id=1)
# 2. 访问数据库，获取相关的 Book 对象
book  = person.book
```

使用 select_related 查找:

```python
# 1. 访问数据库
person = PeopleInfo.objects.select_related('book').get(id=1)

# 2. 不需要访问数据库，因为 person.book 已经被预先填充在上一个查询中
book = person.book
```

使用 select_related() 来处理任何对象的查询集

```python
from django.utils import timezone

# 查找有关 1 号人物的所有的书籍
books = set()

>>> 
>>> for p in ps:
...     p.book
... 

for p in PeopleInfo.objects.filter(id__lt=8).select_related('book'):
    # 如果没有 select_related，将为每个对象创建一个数据库查询
    books.add(ps.book)
```

> filter() 和 select_related() 的顺序并不重要
{.is-success}

多个外键使用 select_related(） 查询，将会缓存多个模型相关的数据，例如：

```python
from django.db import models

class City(models.Model):
    # ...
    pass

class Person(models.Model):
    # ...
    hometown = models.ForeignKey(
        City,
        on_delete=models.SET_NULL,
        blank=True,
        null=True,
    )

class Book(models.Model):
    # ...
    author = models.ForeignKey(Person, on_delete=models.CASCADE)
```

```python
# 将缓存相关的 Person 和 相关的 City：
Book.objects.select_related('author__hometown').get(id=4)
p = b.author         # 不需要访问数据库
c = p.hometown       # 不需要访问数据库
```

如果 需要清除过去调用 select_related 在 QuerySet 上添加的相关字段列表，你可以传递 None 作为参数：

```python
without_relations = queryset.select_related(None)
```

更多写法：链式调用

```python
select_related('foo').select_related('bar')
# 等同于
select_related('foo', 'bar')
```

- `prefetch_related()`：与 select_related 有类似的目的，阻止因访问相关对象而引起的数据库查询潮，但策略却完全不同。

select_related 的工作方式是创建一个 SQL 连接，并在 SELECT 语句中包含相关对象的字段，仅限于单值关系，外键和一对一。

prefetch_related 则对每个关系进行单独的查找，并在 Python 中进行 `joining`，除了支持外键和一对一关系外，还可以预取多对多和多对一的对象

例如，假设你有这些模型：

```python
from django.db import models

class Topping(models.Model):
    name = models.CharField(max_length=30)

class Pizza(models.Model):
    name = models.CharField(max_length=50)
    toppings = models.ManyToManyField(Topping)

    def __str__(self):
        return "%s (%s)" % (
            self.name,
            ", ".join(topping.name for topping in self.toppings.all()),
        )
```

运行

```python
>>> Pizza.objects.all()
["Hawaiian (ham, pineapple)", "Seafood (prawns, smoked salmon)"...
```

这样做的问题是，每次 `Pizza.__str__()` 都要要求 `self.toppings.all()` 查询数据库，导致执行`Pizza.objects.all()` 时， Toppings 表会对 Pizza QuerySet 中的 每项进行查询。

通过使用 prefetch_related 减少到只有两个查询：

```python
Pizza.objects.all().prefetch_related('toppings')
```

这意味着每一个 Pizza 都有一个 `self.toppings.all()`，现在每次调用 `self.toppings.all()` 时，不必再去数据库中寻找这些项目，而是在一次查询中填充的预设 QuerySet 缓存中找到它们。

> `prefetch_related()` 中的附加查询是在 QuerySet 开始执行和主要查询被执行后执行的

# 查询不返回 QuerySet 的方法

| 方法                 | 描述                                                         |
| :------------------- | :----------------------------------------------------------- |
| `get()`              | 返回单个对象。如果查找返回多个对象，则会引发 `Model.MultipleObjectsReturned`，没有找到任何对象，它会引发一个 `Model.DoesNotExist` 异常 |
| `get_or_create()`    | 返回单个对象。如果对象不存在，它将创建一个                   |
| `update_or_create()` | 更新单个对象。如果对象不存在，它将创建一个                   |
| `count()`            | 计算返回的 QuerySet 中的对象数。返回一个整数。背后执行 `SELECT COUNT(*)` |
| `in_bulk()`          | 返回一个包含所有具有列出 ID 的对象的 QuerySet                |
| `iterator()`         | 查询直接读取结果不在 QuerySet 级别做任何缓存，返回一个迭代器，对于返回大量对象的 QuerySet  可以减少内存。 |
| `latest()`           | 根据给定的字段返回数据库表中的最新对象，默认根据 Meta 指定了 get_latest_by，否则根据给定参数                       |
| `earliest()`         | 根据给定的字段返回数据库表中最早的对象                       |
| `first()`            | 返回与 QuerySet 匹配的第一个对象，如果没有匹配的对象，则返回 None。对于没有排序的，查询集自动按主键排序 |
| `last()`             | 返回与 QuerySet 匹配的最后一个对象，如果没有匹配的对象，则返回 None |
| `aggregate()`        | 聚合查询                                                     |
| `exists()`           | QuerySet 是否包含任何结果，包含则返回 True，如果不包含，则返回 False |
| `as_manager()`       | 返回一个`Manager`包含 QuerySet 方法副本的类实例              |
| `explain()`          | 返回 QuerySet 的执行计划的字符串。用于分析查询性能           |


## in_bulk()

接受一个字段值列表 `id_list`，返回字段值与其对应对象的映射字典。其中 `field_name` 必须是一个唯一的字段，它默认为主键

- 如果没有提供 `id_list`，则返回查询集中的所有对象。
- 如果提供一个空列表，你将得到一个空字典。

```python
# eg: Blog.objects.in_bulk([id1,id2])
# res: {1: <obj1>,2:<obj2>}

>>> Blog.objects.in_bulk([1])
{1: <Blog: Beatles Blog>}
>>> Blog.objects.in_bulk([1, 2])
{1: <Blog: Beatles Blog>, 2: <Blog: Cheddar Talk>}
>>> Blog.objects.in_bulk([])
{}
>>> Blog.objects.in_bulk()
{1: <Blog: Beatles Blog>, 2: <Blog: Cheddar Talk>, 3: <Blog: Django Weblog>}
>>> Blog.objects.in_bulk(['beatles_blog'], field_name='slug')
{'beatles_blog': <Blog: Beatles Blog>}
```

## iterator()

查询直接读取结果不在 QuerySet 级别做任何缓存，返回一个迭代器。对于一个只需要访问一次就能返回大量对象的 QuerySet 来说，这可以带来更好的性能，并显著减少内存。

> QuerySet 通常会在内部缓存其结果，默认的迭代器调用 iterator() 并缓存返回值。
{.is-info}

chunk_size 参数控制 Django 从数据库驱动中获取的批次大小。批量越大，就会减少与数据库驱动通信的开销，但代价是略微增加内存消耗。默认值 2000。

> 使用 iterator() 会导致 refetch_related() 调用被忽略，因为这两种优化方式放在一起没有意义。
{.is-warning}

# 添加

## save()

Django 仅当在显式调用 save() 才操作数据库。其背后执行了 `INSERT SQL` 语句

```python
>>> from blog.models import Blog
>>> b = Blog(name='Beatles Blog', tagline='All the latest Beatles news.')
>>> b.save()
```

## create()

创建一个对象并一步到位地保存

```python
>>> Author.objects.create(name="Jack", email="admin@admin.com")
<Author: Jack>
```

与下面的方式等价：

```python
a = Author(name="Jack", email="admin@admin.com")
a.save(force_insert=True) # 强制执行 INSERT
```

## bulk_create()

将所提供的对象列表以高效的方式插入到数据库中

```python
>>> a1 = Author(name = 'Mike',email = 'mike@qq.com')
>>> a2 = Author(name = 'Tom',email = 'tom@163.com')
>>> Author.objects.bulk_create([a1,a2])
[<Author: Mike>, <Author: Tom>]
```

> 有关注意事项参考[官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/querysets/#bulk-create)
{.is-success}

该方法有一个 batch_size 参数控制在一次查询中创建多少对象，默认情况是在一个批次中创建所有对象，但 SQLite 除外，默认情况是每个查询最多使用 999 个变量。

## get_or_create()

一个简便的方法，用于查找特定对象，不存在则创建。

常用来防止在并行进行请求时创建重复的对象，例如：

```python
try:
    obj = Person.objects.get(first_name='John', last_name='Lennon')
except Person.DoesNotExist:
    obj = Person(first_name='John', last_name='Lennon', birthday=date(1940, 10, 9))
    obj.save()
```

如果是并发请求，可能会多次尝试用相同的参数保存一个 Person。为了避免这种竞争条件，可以使用 get_or_create() 重写上面的例子，比如：

```python
obj, created = Person.objects.get_or_create(
    first_name='John',
    last_name='Lennon',
    defaults={'birthday': date(1940, 10, 9)},
)
```

返回 `(object, created)` 的元组，其中 object 是检索或创建的对象，created 是指定是否创建新对象的布尔值。

- 如果找到了一个对象，则 `get_or_create()` 返回该对象的元组和 False。

- 如果找到多个对象，get_or_create() 会引发 `MultipleObjectsReturned`

- 如果没有找到对象，返回一个新对象的元组和 True

> 如果关键字参数中使用的字段有唯一性约束，这个方法是**原子性**的。否则并发调用可能会导致插入具有相同参数的多条记录。
{.is-warning}


**一个复杂的案例**

如果 Robert 或 Bob Marley 存在，则检索 Robert 或 Bob Marley，否则创建后者：

```python
from django.db.models import Q

obj, created = Person.objects.filter(
    Q(first_name='Bob') | Q(first_name='Robert'),
).get_or_create(last_name='Marley', defaults={'first_name': 'Bob'})
```

> 官方推荐只在 POST 请求中使用它，因为 GET 请求不应该对数据有任何影响。
{.is-success}

> 通过 ManyToManyField 属性和反向关系来使用 get_or_create()。在这种情况下，你将限制在该关系的上下文内进行查询。如果你不持续使用它，这可能会导致一些完整性问题。
{.is-warning}

例如：

```python
class Chapter(models.Model):
    title = models.CharField(max_length=255, unique=True)

class Book(models.Model):
    title = models.CharField(max_length=256)
    chapters = models.ManyToManyField(Chapter)
```

可以通过 Book 的 chapters 字段使用 get_or_create()，但它只能在该书的上下文中获取：

```python
>>> book = Book.objects.create(title="Ulysses")
>>> book.chapters.get_or_create(title="Telemachus")
(<Chapter: Telemachus>, True)
>>> book.chapters.get_or_create(title="Telemachus")
(<Chapter: Telemachus>, False)
>>> Chapter.objects.create(title="Chapter 1") # 这一行导致下面出现异常
<Chapter: Chapter 1>
>>> book.chapters.get_or_create(title="Chapter 1")
# Raises IntegrityError 抛出异常
```

# 修改

## update_or_create()

用给定的 kwargs 更新对象的一种方便方法，是必要时创建一个新对象。defaults 是用来更新对象的 (field, value) 对的字典。defaults 中的值可以是可调用对象。

具体的含义与注意事项与 get_or_update() 一样。

例如：

```python
defaults = {'first_name': 'Bob'}
try:
    obj = Person.objects.get(first_name='John', last_name='Lennon')
    for key, value in defaults.items():
        setattr(obj, key, value)
    obj.save()
except Person.DoesNotExist:
    new_values = {'first_name': 'John', 'last_name': 'Lennon'}
    new_values.update(defaults)
    obj = Person(**new_values)
    obj.save()
```

上面的例子可以使用 update_or_create() 重写

```python
obj, created = Person.objects.update_or_create(
    first_name='John', last_name='Lennon',
    defaults={'first_name': 'Bob'},
)
```

## update()

对指定的字段执行 SQL 更新查询，并返回匹配的行数

例如，要关闭 2010 年发表的所有博客条目的评论：

```python
Entry.objects.filter(pub_date__year=2010).update(comments_on=False)
```

如果只是更新一条记录，不需要对模型对象做任何事情，最有效的方法是调用 update()，而不是将模型对象加载到内存中。例如，不要这样做：

```python
e = Entry.objects.get(id=10)
e.comments_on = False
e.save()
```

因为 update() 是在 SQL 级别上进行更新，因此，它不会在模型上调用任何 save() 方法，也不会发出 pre_save 或 post_save 信号

# 删除

## delete()

对 QuerySet 中的所有行执行 SQL 删除查询，并返回删除的对象数量和每个对象类型的删除数量的字典。

```python
>>> b = Blog.objects.get(pk=1)

# Delete all the entries belonging to this Blog.
>>> Entry.objects.filter(blog=b).delete()
(4, {'weblog.Entry': 2, 'weblog.Entry_authors': 2})
```

> 默认情况下，Django 的 ForeignKey 模拟了 SQL 约束 ON DELETE CASCADE
{.is-info}

delete() 方法进行批量删除，会为所有被删除的对象（包括级联删除）发出 pre_delete 和 post_delete 信号。

Django 需要将对象获取到内存中来发送信号和处理级联。但是，如果没有级联和信号，那么 Django 可能会采取**快速路径**删除对象，而不需要将其获取到内存中。对于大面积的删除，这可以使内存使用量大大降低。也可以减少执行查询的数量。

设置为 on_delete=DO_NOTHING 的外键不会阻止在删除时采取快速路径。

# Where 查询条件

> 详细信息参考[官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/querysets/#field-lookups)
{.is-success}


| 条件                       | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| `exact`/`iexact`           | 完全匹配。`iexact`是不区分大小写的版本。如果提供的比较值是 `None`，它将被解释为 SQL `NULL` |
| `contains`/`icontains`     | 字段包含搜索文本。`icontains`是不区分大小写的版本            |
| `in`                       | 在给定的可迭代（列表，元组或 QuerySet ）中                   |
| `gt`/`gte`                 | 大于/大于或等于                                              |
| `lt`/`lte`                 | 小于/小于或等于                                              |
| `startswith`/`istartswith` | 以搜索字符串开头。`istartswith`是不区分大小写的版本          |
| `endswith`/`iendswith`     | 以搜索字符串结尾。`iendswith`是不区分大小写的版本            |
| `range`                    | 范围测试。范围包括开始和结束值                               |
| `date`                     | 将值强制转换为日期。用于日期时间字段查找                     |
| `year`                     | 搜索确切的年份匹配                                           |
| `iso_year`                 | 搜索精确的ISO 8601年匹配项                                   |
| `month`                    | 对于日期和日期时间字段，精确的月份匹配                       |
| `day`                      | 对于日期和日期时间字段，精确匹配日期                         |
| `week`                     | 对于日期和日期时间字段，根据 [ISO-8601](https://en.wikipedia.org/wiki/ISO-8601) ，返回星期号（1-52 或 53） |
| `week_day`                 | 对于日期和日期时间字段，“星期几”匹配                         |
| `quarter`                  | 对于日期和日期时间字段，“一年的四分之一”匹配。               |
| `time`                     | 将值强制转换为时间。用于`datetime`现场查询                   |
| `hour`                     | 搜索精确的小时匹配                                           |
| `minute`                   | 搜索精确的分钟匹配                                           |
| `second`                   | 搜索精确的第二场比赛                                         |
| `isnull`                   | 检查字段是否为空。取`True`或`False`                          |
| `regex`/`iregex`           | 正则表达式匹配。`iregex`是不区分大小写的版本                 |

## 聚合函数

| 函数名     | 描述                           |
| ---------- | ------------------------------ |
| `Avg`      | 返回表达式的平均值。           |
| `Count`    | 计算返回的对象数               |
| `Max`      | 返回表达式的最大值。           |
| `Min`      | 返回表达式的最小值。           |
| `StdDev`   | 返回给定表达式中数据的标准差。 |
| `Sum`      | 返回表达式中所有值的总和。     |
| `Variance` | 返回给定表达式中数据的方差。   |

## aggregate()

```python
from django.db.models import Sum, Count, Max, Min, Avg

### Avg()
>>> Book.objects.all().aggregate(Avg('price'))
{'price__avg': 34.35}

### Max()
>>> Book.objects.all().aggregate(Max('price'))
{'price__max': Decimal('81.20')}

### Min()
>>> Book.objects.all().aggregate(Min('price'))
{'price__min': Decimal('12.99')}

### Count()
>>> Book.objects.all().aggregate(Count('id'))
{'id__count': 5}

### Sum()
>>> Book.objects.aggregate(Sum('bread'))
{'bread__sum': 126}
```

`aggregate()` 函数返回值为一个字典，默认字典的键是根据字段名和聚合函数而自动生成的（字段名_聚合函数）也可以指定的名称。

```python
>>> Book.objects.aggregate(average_price=Avg('price'))
{'average_price': 34.35}
```

如果你想生成更多的聚合内容，你需要在 aggregate() 子句中加入其它参数即可

```python
>>> Book.objects.aggregate(Avg('price'), Max('price'), Min('price'))
{'price__avg': 34.35, 'price__max': Decimal('81.20'), 'price__min': Decimal('12.99')}
```

## annotate()

使用 `annotate()` 子句时 QuerySet 中的每一个对象将对指定值进行汇总。

```python
>>> q = Book.objects.annotate(Count('authors'))
>>> q[0].authors__count
2
>>> q[1].authors__count
1
```

与 aggregate() 一样，注解的名称是根据聚合函数和被聚合的字段名自动生成的，也可以重写这个默认名。

```python
>>> q = Book.objects.annotate(num_authors=Count('authors'))
>>> q[0].num_authors
2
>>> q[1].num_authors
1
```

> 与 `aggregate()` 不同的是，`annotate()` 不是终端子句。`annotate()` 子句的输出就是 QuerySet。这个 QuerySet 被其他 QuerySet 操作进行修改，包括 `filter()`, `order_by()`
{.is-info}

# 查询表达式

> [官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/expressions/#f-expressions)
{.is-success}

## F() 表达式

有时需要在一个字段上执行一个简单的算术任务，比如递增或递减当前值。一种方法是在 Python 中进行运算，比如：

```python
reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed += 1
reporter.save()
```

这一过程是从数据库中提取了 reporter.stories_filed 的值到内存中，使用 Python 操作符对其进行操作，然后将对象保存回数据库

然而使用 F() 对象操作方法如下：

```python
from django.db.models import F

reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed = F('stories_filed') + 1
reporter.save()
```

看似是一个普通的 Python 赋值给一个实例属性 ，实际当 Django 遇到 F() 的实例时，它会覆盖标准的 Python 运算符来创建一个封装的 SQL 表达式，在数据库层面描述所需的操作。Python 层面并不知道 reporter.stories_filed 的值是多少。

要访问这样保存的新值，必须重新加载对象：

```python
reporter = Reporters.objects.get(pk=reporter.pk)
# Or, more succinctly:
reporter.refresh_from_db()
```

F() 还可以与 update() 一起用于对象实例的 QuerySets，从而把上面使用的两个查询 get() 和 save() 减少到只有一个：

```python
reporter = Reporters.objects.filter(name='Tintin')
reporter.update(stories_filed=F('stories_filed') + 1)
```

如果想要递增多个对象上的字段值，可以使用下面方式，这比遍历递增每个对象的字段值，然后把每个对象保存回数据库要快得多：

```python
Reporter.objects.all().update(stories_filed=F('stories_filed') + 1)
```

因此，F() 可以通过以下方式提供性能优势：

- **让数据库，而不是 Python 来完成工作**
- **减少某些操作所需的查询次数**

# 使用 F() 避免竞争条件。

如果两个 Python 线程执行下面的代码，一个线程可以在另一个线程从数据库中获取一个字段的值后，检索、递增并保存它。第二个线程保存的值将基于原始值，从而导致第一个线程的工作并不是基于第二个线程只有的结果。

```python
reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed += 1
reporter.save()
```

如果数据库负责更新字段，那么这个过程就比较稳健：它只会在执行 `save()` 或 `update()` 时，根据数据库中字段的值来更新字段，而不是根据检索实例时的值来更新。

# F() 赋值在 Model.save() 之后持续存在

例如：

```reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed = F('stories_filed') + 1
reporter.save()

reporter.name = 'Tintin Jr.'
reporter.save()python
```

在这种情况下，stories_filed 将被更新两次。如果最初是 1，最终值将是 3。这种持久性可以通过在保存模型对象后重新加载来避免，例如，使用 refresh_from_db()。

# 在过滤器中使用 F()

Django 中 F() 的实例充当查询中的模型字段的引用。这些引用可在查询过滤器中用于在同一模型实例中比较两个不同的字段。

例：查询图书阅读量大于评论量图书信息。

```python
from django.db.models import F
BookInfo.objects.filter(bread__gt=F('bcomment'))
```

例：查询图书阅读量大于2倍评论量图书信息。

```python
BookInfo.objects.filter(bread__gt=F('bcomment')*2)
```

例：查询图书阅读量大于评论量+点赞量图书信息。

```python
BookInfo.objects.filter(bread__gt=F('bcomment')+F('blike'))
```

对于 date 和 datetime 字段，可以加上或减去一个 timedelta 对象。以下会返回所有创建 3 天后被修改的条目:

```python
from datetime import timedelta
BookInfo.objects.filter(modifidy_at__gt=F('created_at') + timedelta(days=3))
```

F() 对象通过 `.bitand()`， `.bitor()`， `.bitxor()`，`.bitrightshift()` 和 `.bitleftshift()` 支持位操作。例子:

```python
F('somefield').bitand(16)
```

> 更多用法，请参考[官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/expressions/#using-f-with-annotations)
{.is-info}

## Q() 对象

像 F() 表达式一样，Q() 对象将 SQL 表达式封装在 Python 对象内部。Q() 对象最常用于通过使用 `AND(&)`、`OR(|)`和 `NOT(~)` 运算符将多个表达式链接在一起来构造复杂的数据库查询：

- 例：查询id大于3且阅读量大于30的图书的信息。

```python
from django.db.models import Q
BookInfo.objects.filter(id__gt=3, bread__gt=30)
BookInfo.objects.filter(Q(id__gt=3)&Q(bread__gt=30))
```

- 例：查询id大于3或者阅读量大于30的图书的信息。

```python
BookInfo.objects.filter(Q(id__gt=3)|Q(bread__gt=30))
```

- 例：查询id不等于3图书的信息。

```python
BookInfo.objects.filter(~Q(id=3))
```


