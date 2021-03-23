---
title: Django 模型
description: 
published: true
date: 2021-03-23T10:17:17.403Z
tags: django
editor: markdown
dateCreated: 2021-02-27T07:41:11.095Z
---

# Django 中的 ORM

传统的数据库操作都是通过编写 SQL 语句，且针对不同的数据库还存在一定的差异。而 ORM 则是通过使用类和对象对数据库进行操作，避免了直接使用 SQL 语句，从而简化对数据库的操作。这种程序技术的底层主要是通过映射机制实现的。

如图是 ORM 与数据库的映射关系图。

```diagram
PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiB2ZXJzaW9uPSIxLjEiIHdpZHRoPSIzODUiIGhlaWdodD0iMjI0IiB2aWV3Qm94PSItMC41IC0wLjUgMzg1IDIyNCIgY29udGVudD0iJmx0O214ZmlsZSBob3N0PSZxdW90O2VtYmVkLmRpYWdyYW1zLm5ldCZxdW90OyBtb2RpZmllZD0mcXVvdDsyMDIxLTAyLTI3VDA4OjAyOjM3Ljg3M1omcXVvdDsgYWdlbnQ9JnF1b3Q7NS4wIChYMTE7IExpbnV4IHg4Nl82NCkgQXBwbGVXZWJLaXQvNTM3LjM2IChLSFRNTCwgbGlrZSBHZWNrbykgQ2hyb21lLzg4LjAuNDMyNC4xODIgU2FmYXJpLzUzNy4zNiZxdW90OyB2ZXJzaW9uPSZxdW90OzE0LjQuMiZxdW90OyBldGFnPSZxdW90O3ExMUtVaWQ5X1FRN1pMZDZLdktMJnF1b3Q7IHR5cGU9JnF1b3Q7ZW1iZWQmcXVvdDsmZ3Q7Jmx0O2RpYWdyYW0gaWQ9JnF1b3Q7RFlFbWladksyTHZmNGhUWWhsdXomcXVvdDsgbmFtZT0mcXVvdDtQYWdlLTEmcXVvdDsmZ3Q7NVpqTGNwc3dGSWFmUnR1T1FPYTJCSXpiUlRPZGFSWk5sd3FTUWEyTVBFS083VDU5SlNNQ0dOekp4STR6Y1RjZXpxK0x4ZmNmbndNR0tGM3RQa3U4THU4RW9SeTRrT3dBbWdQWERWeW9QNDJ3YndUUGR4dWhrSXcwRXV5RWUvYUhOcUxUcWh0R2FHMjFSbEpDY01YV1F6RVhWVVZ6TmRDd2xHSTduTFlVbkF5RU5TN280QmhHdU04eHA2TnBQeGhSWmFPR2J0RHBYeWdyeXZhYkhUOXFSbGE0bld3M3JrdE14TGEzSzhvQVNxVVFxcmxhN1ZMS0Ric2hsOFdKMGVlRFNWcXBseXl3M0o4dzM5aDdzK2RTKy9abXBkaFVoSnI1RGtESnRtU0szcTl4YmthMzJsMnRsV3JGN2ZDU2NaNEtMdVJoTFNLWWhzdGM2N1dTNGpmdGpmaDVTQitYZXNRZWdFcEZkeWR2b2tPalU0cUtGVlZ5cjZmWUJaSGZyTERaNUxRN2JIdmUyQ2xsenhZOTBhYUVUWWZpZWVlT21MNncwS1lCb2hIQWI5L3ZSZ3oxdmFraHFDR1FTbFQwaUo2Vk1HZEZwY05jczZCYVR3d3BwbE14dGdNclJvajVta2xuT3UrZzJWNVV5djZZWEtlTjdTR2RDL2pnT05IUUNJUkdSc3pnaEJId2ZCOW00MFRPQXBBNElFbk96T2dPbWpNYlp6aUVhZXI3cHd5OUJGUVlmdktHV0lOZ2hOVjN4bGlSZno1V2J3S3JCK0lGU0NLUWhZWnY3TndhWDExUXJzYlhuK1Nyc1VZWnlId1FRaEFITjhjM2hGZmpHN3h0ZjFzdXFaOVA5amNTUkk4UVhnWWhDdEg3TmJod1JIQStycWovUjMrYndhTUhqU3YydDJpaVVJU20rTWJobTlhSHhTS0VoengrcS9xQUl1OGQrMXY3UmpIbUdxYTN4dldhZmMxeEpyanFCNGM1aUFMVDJPSU1KTjdOQWI1aVkydExlNDhlSmZvbDFZWkNxbElVb3NJODY5U2pjdG5OK1NyRTJsTDlSWlhhVzZ4NG84U1FPZDB4OWRDNy9tbTIwaFNhYUw2ek94K0NmUnRVK3RZZStrRnZsUW03Wlllb1hYZnNiNjJ3VkxGNWV6ZnRndU82Wm5rckx4aHZqMGh3WFQ2blVNUElnUG0zeVpxajJNamN6ckx2RW5yamdxcGUrUjJuZ3FRY0svWTAzUDBzVzlGSHNYWEErWVhlSEZ2Nk9udThzVDFOR2IrR1A3T1A0czlyZjNidjU2cy80ZXVKRW55dXJ6cnMvbU03alBYK3FFVFpYdz09Jmx0Oy9kaWFncmFtJmd0OyZsdDsvbXhmaWxlJmd0OyI+PGRlZnM+PGZpbHRlciBpZD0iZHJvcFNoYWRvdyI+PGZlR2F1c3NpYW5CbHVyIGluPSJTb3VyY2VBbHBoYSIgc3RkRGV2aWF0aW9uPSIxLjciIHJlc3VsdD0iYmx1ciIvPjxmZU9mZnNldCBpbj0iYmx1ciIgZHg9IjMiIGR5PSIzIiByZXN1bHQ9Im9mZnNldEJsdXIiLz48ZmVGbG9vZCBmbG9vZC1jb2xvcj0iIzNENDU3NCIgZmxvb2Qtb3BhY2l0eT0iMC40IiByZXN1bHQ9Im9mZnNldENvbG9yIi8+PGZlQ29tcG9zaXRlIGluPSJvZmZzZXRDb2xvciIgaW4yPSJvZmZzZXRCbHVyIiBvcGVyYXRvcj0iaW4iIHJlc3VsdD0ib2Zmc2V0Qmx1ciIvPjxmZUJsZW5kIGluPSJTb3VyY2VHcmFwaGljIiBpbjI9Im9mZnNldEJsdXIiLz48L2ZpbHRlcj48L2RlZnM+PGcgZmlsdGVyPSJ1cmwoI2Ryb3BTaGFkb3cpIj48cmVjdCB4PSIwIiB5PSIwIiB3aWR0aD0iODYiIGhlaWdodD0iMjEyIiByeD0iMTIuOSIgcnk9IjEyLjkiIGZpbGw9IiNkYWU4ZmMiIHN0cm9rZT0iIzZjOGViZiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxyZWN0IHg9IjIzIiB5PSIxMiIgd2lkdGg9IjQwIiBoZWlnaHQ9IjIwIiBmaWxsPSJub25lIiBzdHJva2U9Im5vbmUiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogMzhweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAyMnB4OyBtYXJnaW4tbGVmdDogMjRweDsiPjxkaXYgc3R5bGU9ImJveC1zaXppbmc6IGJvcmRlci1ib3g7IGZvbnQtc2l6ZTogMDsgdGV4dC1hbGlnbjogY2VudGVyOyAiPjxkaXYgc3R5bGU9ImRpc3BsYXk6IGlubGluZS1ibG9jazsgZm9udC1zaXplOiAyMXB4OyBmb250LWZhbWlseTogSGVsdmV0aWNhOyBjb2xvcjogIzAwMDAwMDsgbGluZS1oZWlnaHQ6IDEuMjsgcG9pbnRlci1ldmVudHM6IGFsbDsgZm9udC13ZWlnaHQ6IGJvbGQ7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPk9STTwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSI0MyIgeT0iMjgiIGZpbGw9IiMwMDAwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMjFweCIgdGV4dC1hbmNob3I9Im1pZGRsZSIgZm9udC13ZWlnaHQ9ImJvbGQiPk9STTwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgeD0iMTIuNSIgeT0iNTYiIHdpZHRoPSI2MSIgaGVpZ2h0PSIzNiIgcng9IjUuNCIgcnk9IjUuNCIgZmlsbD0iIzAwY2M2NiIgc3Ryb2tlPSJub25lIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDU5cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogNzRweDsgbWFyZ2luLWxlZnQ6IDE0cHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTRweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPuexuzwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSI0MyIgeT0iNzgiIGZpbGw9IiMwMDAwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTRweCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+57G7PC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCB4PSIxMi41IiB5PSIxMDgiIHdpZHRoPSI2MSIgaGVpZ2h0PSIzNiIgcng9IjUuNCIgcnk9IjUuNCIgZmlsbD0iIzAwY2M2NiIgc3Ryb2tlPSJub25lIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDU5cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogMTI2cHg7IG1hcmdpbi1sZWZ0OiAxNHB4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDE0cHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj7lr7nosaE8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iNDMiIHk9IjEzMCIgZmlsbD0iIzAwMDAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxNHB4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj7lr7nosaE8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IHg9IjEyLjUiIHk9IjE1OSIgd2lkdGg9IjYxIiBoZWlnaHQ9IjM2IiByeD0iNS40IiByeT0iNS40IiBmaWxsPSIjMDBjYzY2IiBzdHJva2U9Im5vbmUiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogNTlweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAxNzdweDsgbWFyZ2luLWxlZnQ6IDE0cHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTRweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPuWxnuaApzwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSI0MyIgeT0iMTgxIiBmaWxsPSIjMDAwMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE0cHgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPuWxnuaApzwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgeD0iMjg3IiB5PSIwIiB3aWR0aD0iODYiIGhlaWdodD0iMjEyIiByeD0iMTIuOSIgcnk9IjEyLjkiIGZpbGw9IiNmZmU2Y2MiIHN0cm9rZT0iI2Q3OWIwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxyZWN0IHg9IjMxMCIgeT0iMTIiIHdpZHRoPSI0MCIgaGVpZ2h0PSIyMCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSJub25lIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDM4cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogMjJweDsgbWFyZ2luLWxlZnQ6IDMxMXB4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDIxcHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyBmb250LXdlaWdodDogYm9sZDsgd2hpdGUtc3BhY2U6IG5vcm1hbDsgd29yZC13cmFwOiBub3JtYWw7ICI+REI8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iMzMwIiB5PSIyOCIgZmlsbD0iIzAwMDAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIyMXB4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIiBmb250LXdlaWdodD0iYm9sZCI+REI8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IHg9IjI5OS41IiB5PSI1NiIgd2lkdGg9IjYxIiBoZWlnaHQ9IjM2IiByeD0iNS40IiByeT0iNS40IiBmaWxsPSIjZmY4MDAwIiBzdHJva2U9Im5vbmUiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogNTlweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiA3NHB4OyBtYXJnaW4tbGVmdDogMzAxcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTRweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPuihqDwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSIzMzAiIHk9Ijc4IiBmaWxsPSIjMDAwMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE0cHgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPuihqDwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgeD0iMjk5LjUiIHk9IjEwOCIgd2lkdGg9IjYxIiBoZWlnaHQ9IjM2IiByeD0iNS40IiByeT0iNS40IiBmaWxsPSIjZmY4MDAwIiBzdHJva2U9Im5vbmUiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogNTlweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAxMjZweDsgbWFyZ2luLWxlZnQ6IDMwMXB4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDE0cHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj7ooYw8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iMzMwIiB5PSIxMzAiIGZpbGw9IiMwMDAwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTRweCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+6KGMPC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCB4PSIyOTkuNSIgeT0iMTU5IiB3aWR0aD0iNjEiIGhlaWdodD0iMzYiIHJ4PSI1LjQiIHJ5PSI1LjQiIGZpbGw9IiNmZjgwMDAiIHN0cm9rZT0ibm9uZSIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2g+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OiB2aXNpYmxlOyB0ZXh0LWFsaWduOiBsZWZ0OyIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6IGZsZXg7IGFsaWduLWl0ZW1zOiB1bnNhZmUgY2VudGVyOyBqdXN0aWZ5LWNvbnRlbnQ6IHVuc2FmZSBjZW50ZXI7IHdpZHRoOiA1OXB4OyBoZWlnaHQ6IDFweDsgcGFkZGluZy10b3A6IDE3N3B4OyBtYXJnaW4tbGVmdDogMzAxcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTRweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPuWtl+autTwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSIzMzAiIHk9IjE4MSIgZmlsbD0iIzAwMDAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxNHB4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj7lrZfmrrU8L3RleHQ+PC9zd2l0Y2g+PC9nPjxwYXRoIGQ9Ik0gNzkuODcgNzQgTCAyOTMuMTMgNzQiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBzdHJva2UtZGFzaGFycmF5PSIzIDMiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBkPSJNIDc0LjYyIDc0IEwgODEuNjIgNzAuNSBMIDc5Ljg3IDc0IEwgODEuNjIgNzcuNSBaIiBmaWxsPSIjMDAwMDAwIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGQ9Ik0gMjk4LjM4IDc0IEwgMjkxLjM4IDc3LjUgTCAyOTMuMTMgNzQgTCAyOTEuMzggNzAuNSBaIiBmaWxsPSIjMDAwMDAwIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGQ9Ik0gNzkuODcgMTI2IEwgMjkzLjEzIDEyNiIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHN0cm9rZS1kYXNoYXJyYXk9IjMgMyIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGQ9Ik0gNzQuNjIgMTI2IEwgODEuNjIgMTIyLjUgTCA3OS44NyAxMjYgTCA4MS42MiAxMjkuNSBaIiBmaWxsPSIjMDAwMDAwIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGQ9Ik0gMjk4LjM4IDEyNiBMIDI5MS4zOCAxMjkuNSBMIDI5My4xMyAxMjYgTCAyOTEuMzggMTIyLjUgWiIgZmlsbD0iIzAwMDAwMCIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBkPSJNIDc5Ljg3IDE3NyBMIDI5My4xMyAxNzciIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBzdHJva2UtZGFzaGFycmF5PSIzIDMiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBkPSJNIDc0LjYyIDE3NyBMIDgxLjYyIDE3My41IEwgNzkuODcgMTc3IEwgODEuNjIgMTgwLjUgWiIgZmlsbD0iIzAwMDAwMCIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBkPSJNIDI5OC4zOCAxNzcgTCAyOTEuMzggMTgwLjUgTCAyOTMuMTMgMTc3IEwgMjkxLjM4IDE3My41IFoiIGZpbGw9IiMwMDAwMDAiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PC9nPjxzd2l0Y2g+PGcgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ii8+PGEgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoMCwtNSkiIHhsaW5rOmhyZWY9Imh0dHBzOi8vd3d3LmRpYWdyYW1zLm5ldC9kb2MvZmFxL3N2Zy1leHBvcnQtdGV4dC1wcm9ibGVtcyIgdGFyZ2V0PSJfYmxhbmsiPjx0ZXh0IHRleHQtYW5jaG9yPSJtaWRkbGUiIGZvbnQtc2l6ZT0iMTBweCIgeD0iNTAlIiB5PSIxMDAlIj5WaWV3ZXIgZG9lcyBub3Qgc3VwcG9ydCBmdWxsIFNWRyAxLjE8L3RleHQ+PC9hPjwvc3dpdGNoPjwvc3ZnPg==
```

- 模型：相当于 Python 的类，映射一张数据库表。继承自 `django.db.models.Model`
- 属性：相当于一个数据库的字段，映射为数据库的列。每个属性都是一个 `Field` 类的实例
- 对象：相当于数据库中的一行记录

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

# 模型字段类型

> [字段类型参考文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#model-field-types)
{.is-success}

模型中每一个字段都是某个 Field 类的实例，Django 利用这些字段类来实现以下功能：

- 字段类型用以指定数据库数据类型
- 在渲染表单字段时默认使用的 HTML 视图 (如：`<input type="text">, <select>`)。
- 基本的有效性验证功能，用于 Django 后台和自动生成的表单。

| 字段类型                    | 数据库类型                | 描述                                                         |
| --------------------------- | ------------------------- | ------------------------------------------------------------ |
| `AutoField`                 | `int auto_increment`      | 32 位整数自增，默认自增主键                                  |
| `SmallAutoField`            | `smallint auto_increment` | 16 位整数自增，1 到 32767 的值                               |
| `BigAutoField`              | `bigint auto_increment`   | 64 位整数自增，1 到 9223372036854775807                      |
| `SmallIntegerField`         | `smallint `               | 16 位整数，-32768 到 32767的值                               |
| `IntegerField`              | `int`                     | 32 位整数。-2147483648 到 2147483647 的值                    |
| `BigIntegerField`           | `bigint`                  | 64 位整数。-9223372036854775808 到 9223372036854775807 的数字 |
| `PositiveSmallIntegerField` | `int unsigned `           | 0 到 32767 的值                                              |
| `PositiveIntegerField`      | `smallint unsigned`       | 0 到 2147483647 的值                                         |
| `PositiveBigIntegerField`   | `bigint unsigned`         | 0 到 223372036854775807 的值                                 |
| `TimeField`                 | `time(6) `                | 时间，在 Python 中用 `datetime.time` 实例表示<br>默认的表单部件是一个`TimeInput` |
| `DateTimeField`             | `datetime(6)`             | 日期和时间，在 Python 中用一个 `datetime.datetime` 实例表示<br>默认表单部件是一个单独的 `DateTimeInput` |
| `DateField`                 | `date`                    | 日期，在 Python 中用一个 `datetime.date` 实例表示            |
| `DurationField`             | `bigint`                  | 用于存储时间段的字段，在 Python 中用 `timedelta`建模         |
| `CharField(max_length)`     | `varchar(20) `            | 字符串字段。默认表单部件是一个`TextInput`                    |
| `TextField`                 | `longtext`                | 大的文本字段。该字段的默认表单部件是一个 `Textarea`          |
| `FileField`                 | `varchar(100) `           | 文件上传字段                                                 |
| `FilePathField`             | `varchar(100)`            | 一个`CharField`，限定只能在文件系统中的特定目录里选择文件    |
| `ImageField`                | `varchar(100)`            | 继承`FileField`的所有属性和方法，此外验证上传的对象是不是有效的图像。增加了 height 和 width 两个属性。需要 Pillow 库支持 |
| `DecimalField`              | `decimal `                | 固定精度的小数，在 Python 中使用 Decimal 实例表示。有两个必须的参数：`max_digits` 和 `decimal_places` |
| `FloatField`                | `double`                  | 在 Python 中用一个 `float` 实例表示，注意，`field.localize` 的值为 False 时，默认的小组件是 `TextInput` |
| `BooleanField`              | `tinyint(1)`              | `true / false` 字段，该字段的默认表单部件是 `CheckboxInput`  |
| `BinaryField`               | `longblob`                | 用于存储原始二进制数据的字段                                 |
| `EmailField`                | `varchar(254)`            | 是否为有效的电子邮件地址                                     |
| `URLField`                  | `varchar(200)`            | 该字段的默认表单部件是一个`URLInput`                         |
| `UUIDField`                 | `char(32)`                | 存储通用唯一标识符的字段。使用 Python 的`UUID`类             |
| `GenericIPAddressField`     | `char(39)`                | `IPv4` 或 `IPv6` 地址，字符串形式（如 192.0.2.30、2a02:42fe::4） |
| `SlugField`                 | `varchar(50)`             | 简短的标签，只包含字母、数字、下划线或连字符                 |
| `JSONField(Django 3.1)`     | `json`                    | 存储 JSON 编码数据的字段                                     |


# 模型字段选项

> [字段选项参考文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#common-model-field-options)
{.is-success}

## null

默认值为 False ，此时保存模型时，Django 会在数据库的对应字段中保存空。

对于文本型字段，尽可能不使用 null 属性，因为当使用默认值 null 时，数据库中就可能出现两种空数据： NULL 和空字符串，而 Django 默认使用空字符串

## blank

默认为 False，如果一个字段有 blank=True，表单验证将允许输入一个空值。如果一个字段有 blank=False，则该字段为必填字段

## choices

属性值为一个可迭代对象，如列表或者元组，迭代对象的每个成员包括两个字符串。当字段设置了 choices 属性时，字段在网页中将会以下拉列表的形式显示。

列表或元组的第一个值将作为字段值保存到数据库中，第二个值用于提高字段的可读性。使用 `get_FOO_display()` 方法可以获取第二个值

```python
class Student(models.Model):
    FRESHMAN = 'FR'
    SOPHOMORE = 'SO'
    JUNIOR = 'JR'
    SENIOR = 'SR'
    GRADUATE = 'GR'
    YEAR_IN_SCHOOL_CHOICES = [
        (FRESHMAN, 'Freshman'),
        (SOPHOMORE, 'Sophomore'),
        (JUNIOR, 'Junior'),
        (SENIOR, 'Senior'),
        (GRADUATE, 'Graduate'),
    ]
    year_in_school = models.CharField(
        max_length=2,
        choices=YEAR_IN_SCHOOL_CHOICES,
        default=FRESHMAN,
    )
```

![choices-1.png](/assets/web框架/django/字段属性/choices-1.png)

```python
MEDIA_CHOICES = [
    ('Audio', (
        ('vinyl', 'Vinyl'),
        ('cd', 'CD'),
    )
     ),
    ('Video', (
        ('vhs', 'VHS Tape'),
        ('dvd', 'DVD'),
    )
     ),
    ('unknown', 'Unknown'),
]


class GroupChoice(models.Model):
    group_choice_field = models.CharField(max_length=2, choices=MEDIA_CHOICES, default='Audio')
```

![choices-2.png](/assets/web框架/django/字段属性/choices-2.png)

```python
class GroupChoice2(models.Model):
    group_choice_field = models.CharField(max_length=7, blank=True, choices=MEDIA_CHOICES,
                                          default='Audio')
```

![choices-3.png](/assets/web框架/django/字段属性/choices-3.png)

```python
MEDIA_CHOICES = [
    (None, 'Please Select Media'),
    ('Audio', (
        ('vinyl', 'Vinyl'),
        ('cd', 'CD'),
    )
     ),
    ('Video', (
        ('vhs', 'VHS Tape'),
        ('dvd', 'DVD'),
    )
     ),
    ('unknown', 'Unknown'),
]
```

![choices-4.png](/assets/web框架/django/字段属性/choices-4.png)

## default

设置字段默认值。属性值可以是字符串也可以是方法。默认值不可以是可变对象，如：列表 

## help_text

HTML 元素的提示文本，在文本中可以使用 HTML 标记。

例如：

```python
help_text = "Please select your <em>favorite</em> media"


class GroupChoice(models.Model):
    group_choice_field = models.CharField(max_length=7, choices=MEDIA_CHOICES, default='Audio',help_text=help_text)
```

![help_text.png](/assets/web框架/django/字段属性/help_text.png)

## primary_key

将字段设置为数据表主键。如果模型中任何字段都不包含 `primary_key=True` 属性，Django 会自动添加一个 AutoField 来保存主键。

primary_key=True 意味着 null=False 和 unique=True。一个对象只允许有一个主键。

主键永远是只读的，当修改一个模型对象的主键后，如果保存将会在数据库中创建一个新对象

## unique

当宇段的 unique 属性设置为 True 时，该字段的所有值在整张数据表中不能重复，每一行数据都必须有唯一的字段值

如果你试图保存一个在 unique 字段中存在重复值的模型，模型的 save() 方法将引发 `django.db.IntegrityError`。

## verbose_name

verbose_name 属性类似于字段的说明。除了 ForeignKey， ManyToManyField 和 OneToOneField，其他宇段类型都包含一个默认的 verbose_name 属性，如果未指定该参数值，Django 会自动使用字段的属性名作为该参数值，并且把下划线转换为空格。

```python
first_name = models.CharField("person's first name", max_length=30)
```

ForeignKey、ManyToManyField、OneToOneField 三种字段类型要求第 一个参数必须是模型类，因此必须使用 `verbose_name` 关键字

```python
key= models.ForeignKey(Question, verbose_name=' Foreign key')
```

## 其他

| 字段选项           | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `db_column`        | 要使用的数据库列名。如果没有给出列名，Django 将使用**字段名**。 |
| `db_index`         | `db_index=True`为该字段创建数据库索引。                      |
| `db_tablespace`    | 如果这个字段有索引，那么要为这个字段的索引使用的 [数据库表空间](https://docs.djangoproject.com/zh-hans/3.1/topics/db/tablespaces/) 的名称。默认是项目的 [`DEFAULT_INDEX_TABLESPACE`](https://docs.djangoproject.com/zh-hans/3.1/ref/settings/#std:setting-DEFAULT_INDEX_TABLESPACE) 设置（如果有设置），或者是模型的 [`db_tablespace`](https://docs.djangoproject.com/zh-hans/3.1/ref/models/options/#django.db.models.Options.db_tablespace) （如果有）。如果后端不支持索引的表空间，则忽略此选项。 |
| `editable`         | 如果是 `False`，该字段将不会在管理或任何其他`ModelForm`中显示。在模型验证中也会跳过。默认为 `True`。 |
| `error_messages`   | 覆盖该字段引发的默认消息。传入一个与你想覆盖的错误信息相匹配的键值的字典。 |
| `unique_for_date`  | 将其设置为 `DateField`或 `DateTimeField`的名称，要求该字段的日期字段值是唯一的。 |
| `unique_for_month` | 像 `unique_for_date`一样，但要求字段对月份是唯一的           |
| `unique_for_year`  | 同上                                                         |
| `validators`       | 要为该字段运行的验证器列表。                                 |


# 模型 Meta 选项

## abstract

在 Meta 类中填入 `abstract=True`。该模型将不会创建任何数据表。当其用作其它模型类的基类时，它的字段会自动添加至子类。

```python
from django.db import models

class CommonInfo(models.Model):
    name = models.CharField(max_length=100)
    age = models.PositiveIntegerField()

    class Meta:
        abstract = True

class Student(CommonInfo):
    home_group = models.CharField(max_length=5)
```

## app_label

如果模型定义没有注册到 INSTALLED_APPS ，那么就必须使用 `app_label` 选项在 Meta 类指定所属的应用程序名字

## base_manager_name

## db_table

默认情况下一个模型的数据库表名为 `应用名_小写模型名`。通过 `db_table` 可以指定用于模型的数据库表的名称，强烈建议使用小写的表名

```python
class Student(modes.Model):
    # ...
    class Meta:
        db_table = 'student_info'
```

## db_tablespace

指定模型要使用的**数据库表空间**名称。默认是项目的 `DEFAULT_TABLESPACE` 配置。如果后端不支持表空间，则忽略此选项。

## default_manager_name

模型的 `_default_manager` 管理器名称

## default_related_name

关联的对象回指这个模型默认使用的名称。默认为 `<model_name>_set`。

## get_latest_by

该属性的值是字段名或字段名列表，通常是 DateField，DateTimeField 或 IntegerField。使用该参数可以修改默认`last()` 和 `earliest()` 方法的过滤规则。

例如：

```python
# Latest by ascending order_date.
get_latest_by = "order_date"

# Latest by priority descending, order_date ascending.
get_latest_by = ['-priority', 'order_date']
```

## managed

默认为 True，即让 Django 在迁移中创建适当的数据库表，并在执行 flush 管理命令时把表删除。

## order_with_respect_to

标记对象为可排序的，排序依据是指定的字段。

## ordering

用于获取对象列表时，对象的默认排序：

```python
ordering = ['-order_date'] # 降序
ordering = ['order_date'] # 升序
ordering = '?' # 随机排序
ordering = ['-pub_date', 'author'] # 按 pub_date 降序，然后按 author 升序
```

## permissions

创建此对象时，要在权限表中输入额外的权限。 将为每个模型自动创建添加，更改，删除和查看权限。

```python
    class Meta:
        permissions = [
            ("export", "Can export candidate list"),
            ("notify", "notify interviewer for candidate review"),
        ]
```

## default_permissions

默认值为 `('add', 'change', 'delete', 'view')` 。你可以自定义这个列表，例如，如果你的应用不需要任何默认的权限，可以将其设置为空列表。它必须在模型创建之前通过 migrate 在模型上指定，以防止任何遗漏的权限被创建。


## proxy

如果 `proxy = True`，作为另一个模型子类的模型将被视为代理模型。


## indexes

用来定义数据库索引，形式如下：

```python
class Customer(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)

    class Meta:
        indexes = [
            models.Index(fields=['last_name', 'first_name']),
            models.Index(fields=['first_name'], name='first_name_idx')
        ]
```

## unique_together

为数据库表设置联合主键，形式如下：

```python
unique_together = (("driver", "restaurant"),)
```

联合主键是一个由元组组成的元组，每一个元组中的字段在数据库中的值的组合必须是唯一的

如果只有一个联合主键，可以简化 unique_together，例如

```python
unique_together = ['driver', 'restaurant']
```

## index_together

创建联合唯一索引

```python
index_together = ["pub_date", "deadline"]
```

## constraints

创建约束

```python
from django.db import models

class Customer(models.Model):
    age = models.IntegerField()

    class Meta:
        constraints = [
            models.CheckConstraint(check=models.Q(age__gte=18), name='age_gte_18'),
        ]
```

## verbose_name

对象的可读名称，单数：

```python
verbose_name = "pizza"
```

如果没有给定，Django 将使用一个 munged 版本的类名：CamelCase 变成 camel case。


## verbose_name_plural

对象的复数名称：

```python
verbose_name_plural = "stories"
```
如果没有给定，Django 将使用 `verbose_name + "s"`。


## label（只读）

对象的表示，返回 `app_label.object_name`，例如 `'polls.Question'`。

## label_lower（只读）

模型的表示，返回 `app_label.model_name`，例如 `'polls.question'`。


# 外键


外键的存在使得 ORM 框架在处理表关系的时候异常的强大，类定义为 `class ForeignKey(to,on_delete,**options)`：

1. `to`：指示引用的是哪个模型
2. `on_delete`：当外键引用的模型数据被删除了，这个字段该如何处理

比如 person app 中有一个 User 和一个 Article 两个模型，一个 User 可以发表多篇文章，一个 Article 只能有一个 Author，并且通过外键进行引用

```python
class User(models.Model):
    username = models.CharField(max_length=20)
    password = models.CharField(max_length=100)

class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    author = models.ForeignKey(User,on_delete=models.CASCADE)
```

上述的模型对应 MySQL 的 SQL 建表语句如下：

```sql
CREATE TABLE `person_user`
(
    `id`       int          NOT NULL AUTO_INCREMENT,
    `username` varchar(20)  NOT NULL,
    `password` varchar(100) NOT NULL,
    PRIMARY KEY (`id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8
  
CREATE TABLE `person_article`
(
    `id`        int          NOT NULL AUTO_INCREMENT,
    `title`     varchar(100) NOT NULL,
    `content`   longtext     NOT NULL,
    `author_id` int          NOT NULL,
    PRIMARY KEY (`id`),
    KEY `person_article_author_id_61f24638_fk_person_user_id` (`author_id`),
    CONSTRAINT `person_article_author_id_61f24638_fk_person_user_id` FOREIGN KEY (`author_id`) REFERENCES `person_user` (`id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8
```

> 默认情况下，数据库表名为`appname_modelname`，ForeignKey 命名为`fieldname_id`，且会自动创建一个数据库索引。你可以通过设置 `db_index` 为 False 来禁用它。
{.is-info}

如果模型 User 位于模型 Article 之后定义，此时使用名称而不是模型类对象本身引用外键

```python
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    # 使用模型名称
    author = models.ForeignKey("User",on_delete=models.CASCADE)
    
class User(models.Model):
    username = models.CharField(max_length=20)
    password = models.CharField(max_length=100)

```

如果想要引用另外一个 app 的模型，那么应该在传递 to 参数的时候，使用 `app.model_name` 进行指定。

```python
# user/models.py ---> User
class User(models.Model):
    username = models.CharField(max_length=20)
    password = models.CharField(max_length=100)

# article/models.py ---> Article
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
		# 通过 app.model_name 指定
    author = models.ForeignKey("user.User",on_delete=models.CASCADE)
```

如果模型的外键引用的是本身自己这个模型，那么 to 参数可以为 `'self'`，或者是这个模型的名字。例如一般的评论模型即采用自关联的方式

```python
class Comment(models.Model):
    content = models.TextField()
    origin_comment = models.ForeignKey('self',on_delete=models.CASCADE,null=True)
    # 或者
    # origin_comment = models.ForeignKey('Comment',on_delete=models.CASCADE,null=True)
```

## 外键字段选项

### on_delete

- `CASCADE`：级联操作。如果外键对应的那条数据被删除了，那么这条数据也会被删除
- `PROTECT`：受保护。即只要这条数据引用了外键的那条数据，那么就不能删除外键的那条数据。
- `SET_NULL`：设置为空。如果外键的那条数据被删除了，那么在本条数据上就将这个字段设置为空。前提是要指定这个字段可以为空。
- `SET_DEFAULT`：设置默认值。如果外键的那条数据被删除了，那么本条数据上就将这个字段设置为默认值。前提是要指定这个字段一个默认值。
- `SET()`：如果外键的那条数据被删除了。那么将会获取 SET 函数中的值来作为这个外键的值。SET 函数可以接收一个可以调用的对象（比如函数或者方法），如果是可以调用的对象，那么会将这个对象调用后的结果作为值返回回去。
- `DO_NOTHING`：不采取任何行为。一切全看数据库级别的约束。

> 以上这些选项只是 Django 级别的，数据级别依旧是 RESTRICT！但是数据仍然能够被删除，这是因为 Django 发现了外键约束，会优先把所有关联外键的数据删除，然后再把外键所对应的数据删除。

### limit_choices_to

使用 ModelForm 渲染或在管理后台中显示时，限制罗列这个字段的条件（默认为查询集中的所有对象）

```python
staff_member = models.ForeignKey(
    User,
    on_delete=models.CASCADE,
    limit_choices_to={'is_staff': True},
)
```

此时，ModelForm 中的相应字段只会列出 `is_staff=True` 的 User 对象。这对 Django 的管理后台可能有用。结合 Python 的 datetime 模块限制选择的日期范围时可以使用可调用对象。例如：

```python
def limit_pub_date_choices():
		return {'pub_date__lte': datetime.date.utcnow()}
limit_choices_to = limit_pub_date_choices
```

### related_name

```python
class User(models.Model):
    name = models.CharField(max_length=20)
    group = models.ForeignKey('Group', on_delete=models.CASCADE)

class Group(models.Model):
    title = models.CharField(max_length=20)
```

```python
>>> User.objects.all()
<QuerySet [<User: Jack>, <User: Tom>, <User: Mike>]>
>>> Group.objects.all()
<QuerySet [<Group: 技术>, <Group: 产品>]>
```

如果一个 User 想要访问其所在的 Group，那么可以通过 ForeignKey group 来进行访问。

```python
>>> user = User.objects.get(id=1)
>>> user.name
'Jack'
>>> user.group
<Group: 技术>
```

但是如果一个 Group 想要访问其所有的 User 怎么办？

1. 可以通过下面的方法：`模型名字小写_set`

```python
>>> group = Group.objects.get(id=1)
>>> group.title
'技术'
>>> group.user_set.all()
<QuerySet [<User: Jack>, <User: Tom>]>
```

2. 给 ForeignKey 增加 related_name 属性，相当于替代 user_set

```python
class User(models.Model):
    name = models.CharField(max_length=20)
    group = models.ForeignKey('Group', on_delete=models.CASCADE, related_name='users')

>>> group = Group.objects.get(id=1)
>>> group.title
'技术'
>>> group.users.all() # 使用 related_name
<QuerySet [<User: Jack>, <User: Tom>]>
```

> 如果你不希望创建一个反向关系，可以将 `related_name` 设置为 `'+'` 或者以 `'+'` 结束
{.is-warning}


##@ related_query_name

目标模型中反向过滤器的名称。

```python
# 规则：ForeignKey 关联字段__关联模型字段
>>> group = Group.objects.filter(user__name='Jack')
>>> group
<QuerySet [<Group: 技术>]>
```

如果设置了 related_name = 'users' 那么上例代码将改成如下：

```python
# 使用 related_name = 'users'
>>> group = Group.objects.filter(users__name='Jack')
>>> group
<QuerySet [<Group: 技术>]>
```

其次，related_name 的值也默认是 related_query_name 的值。但是 related_query_name 也可以将查询的反转名字修改成其他的名字，比如修改为 users2：

```python
# 使用 related_query_name = 'users2'
>>> group = Group.objects.filter(users2__name='Jack')
>>> group
<QuerySet [<Group: 技术>]>
```

### to_field

指定关联对象的字段。默认情况下，Django 使用相关对象的主键。如果引用了其他字段，这个字段必须是 `unique=True`。

```python
class User(models.Model):
    name = models.CharField(max_length=20)
    # 关联 title 字段
    group = models.ForeignKey('Group', on_delete=models.CASCADE,to_field='title')
    
class Group(models.Model):
		# 被关联的字段必须唯一
    title = models.CharField(max_length=20, unique=True)
```

> 其他更多属性，请参考[官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#django.db.models.ForeignKey.db_constraint)
{.is-info}

# 表关系

表之间的关系都是通过外键来进行关联的，而表之间的关系有三种：一对一、一对多（多对一）、多对多等

## 多对一

- 应用场景

比如文章和作者之间的关系。一个文章只能由一个作者编写，但是一个作者可以写多篇文章。文章和作者之间的关系就是典型的多对一的关系。

- 实现方式

一对多或者多对一，都是通过 `ForeignKey` 来实现的

```python
class User(models.Model):
     username = models.CharField(max_length=20)
     password = models.CharField(max_length=100)

 class Article(models.Model):
     title = models.CharField(max_length=100)
     content = models.TextField()
     author = models.ForeignKey("User",on_delete=models.CASCADE)
```

```sql
CREATE TABLE `relation_user`
(
    `id`       int          NOT NULL AUTO_INCREMENT,
    `username` varchar(20)  NOT NULL,
    `password` varchar(100) NOT NULL,
    PRIMARY KEY (`id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8
  
CREATE TABLE `relation_article`
(
    `id`        int          NOT NULL AUTO_INCREMENT,
    `title`     varchar(100) NOT NULL,
    `content`   longtext     NOT NULL,
    `author_id` int          NOT NULL,
    PRIMARY KEY (`id`),
    KEY `relation_article_author_id_ce671c0d_fk_relation_user_id` (`author_id`),
    CONSTRAINT `relation_article_author_id_ce671c0d_fk_relation_user_id` FOREIGN KEY (`author_id`) REFERENCES `relation_user` (`id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8
```

## 一对一

- 应用场景

比如一个用户表和一个用户信息表。用户信息表用于对用户表的信息补充，因此可以把用户的一些不常用的信息存放到另外一张表中我们叫做 UserExtension。而用户表 User 和用户信息表 UserExtension 就是典型的一对一了。

- 实现方式

Django 为一对一提供了一个专门的 Field 叫做 OneToOneField 来实现一对一操作

```python
class User(models.Model):
     username = models.CharField(max_length=20)
     password = models.CharField(max_length=100)

 class UserExtension(models.Model):  
     birthday = models.DateTimeField(null=True)  
     school = models.CharField(blank=True,max_length=50)  
     user = models.OneToOneField("User", on_delete=models.CASCADE)
```

```sql
CREATE TABLE `relation_userextension`
(
    `id`       int         NOT NULL AUTO_INCREMENT,
    `birthday` datetime(6) DEFAULT NULL,
    `school`   varchar(50) NOT NULL,
    `user_id`  int         NOT NULL,
    PRIMARY KEY (`id`),
    UNIQUE KEY `user_id` (`user_id`),
    CONSTRAINT `relation_userextension_user_id_c4a7f31a_fk_relation_user_id` FOREIGN KEY (`user_id`) REFERENCES `relation_user` (`id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8
```

在 UserExtension 模型上增加了一个一对一的关系映射。其实底层是在 UserExtension 这个表上增加了一个 user_id，来和 user 表进行关联，并且这个外键数据在表中必须是唯一的，来保证一对一。


## 多对多


- 应用场景

比如文章和标签的关系。一篇文章可以有多个标签，一个标签可以被多个文章所引用。因此标签和文章的关系是典型的多对多的关系。

- 实现方式

Django 为这种多对多的实现提供了专门的 Field。叫做 ManyToManyField

```python
class Article(models.Model):
     title = models.CharField(max_length=100)
     content = models.TextField()
     tags = models.ManyToManyField("Tag",related_name="articles")

 class Tag(models.Model):
     name = models.CharField(max_length=50)
```

```sql
CREATE TABLE `relation_article`
(
    `id`      int          NOT NULL AUTO_INCREMENT,
    `title`   varchar(100) NOT NULL,
    `content` longtext     NOT NULL,
    PRIMARY KEY (`id`)
) ENGINE = InnoDB
  AUTO_INCREMENT = 2
  DEFAULT CHARSET = utf8
  
CREATE TABLE `relation_tag`
(
    `id`   int         NOT NULL AUTO_INCREMENT,
    `name` varchar(50) NOT NULL,
    PRIMARY KEY (`id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8
  
CREATE TABLE `relation_article_tags`
(
    `id`         int NOT NULL AUTO_INCREMENT,
    `article_id` int NOT NULL,
    `tag_id`     int NOT NULL,
    PRIMARY KEY (`id`),
    UNIQUE KEY `relation_article_tags_article_id_tag_id_206d9d17_uniq` (`article_id`, `tag_id`),
    KEY `relation_article_tags_tag_id_0e95cb96_fk_relation_tag_id` (`tag_id`),
    CONSTRAINT `relation_article_tags_article_id_3ddc3832_fk_relation_article_id` FOREIGN KEY (`article_id`) REFERENCES `relation_article` (`id`),
    CONSTRAINT `relation_article_tags_tag_id_0e95cb96_fk_relation_tag_id` FOREIGN KEY (`tag_id`) REFERENCES `relation_tag` (`id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8
```

多对多的关系。需要一个位置参数：模型相关的类。但是其背后 Django 会创建了一个中间连接表来表示多对多的关系。

默认情况下，这个表名是使用多对多字段的名称和包含它的模型的表名生成的，比如下面的：`relation_article_tags`。由于有些数据库不支持超过一定长度的表名，这些表名将被自动截断，并使用唯一性哈希，例如 `article_tags_9cdf` 。不过也可以使用 `db_table` 选项自定义中间表的名称。


ManyToManyField 所涉及的一些参数用法与 ForeignKey 相同。除了个别特有的参数。

> 注意：使用多对多关系由于无法确定反向查询的名称，两个模型中至少一个设置 `related_name`。如果不希望 Django 不创建反向关系，将 `related_name` 设置为 `'+'`。
{.is-warning}

## 多对多额外字段选项

### through

默认情况下，Django 会自动生成一个中间表来管理多对多关系：

- 当源模型和目标模型不同，如下：

```python
class Publication(models.Model):
    title = models.CharField(max_length=30)


class Article(models.Model):
    headline = models.CharField(max_length=100)
    publications = models.ManyToManyField(Publication)

# >>>>>>>>>>>>>  会生成如下字段
++++++++++++++++++++++++++++++++++++++++++++++++
id             int auto_increment primary key,
article_id     int not null,
publication_id int not null,
++++++++++++++++++++++++++++++++++++++++++++++++
```

- 当源模型和目标模型相同，也就是自关联多对多，如下：

```python
class People(models.Model):
    friends = models.ManyToManyField("self")
    
# >>>>>>>>>>>>>  会生成如下字段
++++++++++++++++++++++++++++++++++++++++++++++++   
id             int auto_increment primary key,
from_people_id int not null,
to_people_id   int not null,
++++++++++++++++++++++++++++++++++++++++++++++++
```

当然也可以使用 `through` 参数自定义中间表。其通常目的是为了给中间表添加额外字段而使用。

```python
class Publication(models.Model):
    title = models.CharField(max_length=30)


class Article(models.Model):
    headline = models.CharField(max_length=100)
    # through 指定中间表
    publications = models.ManyToManyField(Publication,through='ArticlePublication')


class ArticlePublication(models.Model):
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
    publication = models.ForeignKey(Publication, on_delete=models.CASCADE)
    # 添加额外字段
    pub_date = models.DateTimeField(default=datetime.now)

    class Meta:
        # 自定义中间表名
        db_table = "article_publication_relationship"
```

### through_fields

只有当指定了一个自定义的中间模型时才会使用，Django 通常会决定使用中介模型的哪些字段来自动建立多对多的关系。如下模型：

```python
class Publication(models.Model):
    title = models.CharField(max_length=30)

    def __str__(self):
        return self.title


class Article(models.Model):
    headline = models.CharField(max_length=100)
    publications = models.ManyToManyField(Publication, through='ArticlePublication',
                                          through_fields=('article', 'publication'))

class ArticlePublication(models.Model):
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
    publication = models.ForeignKey(Publication, on_delete=models.CASCADE)
    # 添加额外字段
    pub_date = models.DateTimeField(default=datetime.now)

    class Meta:
        # 自定义中间表名
        db_table = "article_publication_relationship"
```

`through_fields` 接受一个二元元组 `('field1', 'field2')`，其中 `field1` 是定义在 `ManyToManyField` 上的模型（本例中为 `article` ）的外键名称，`field2` 是目标模型（本例中为 `publication`）的外键名称。


> 其他更多属性，请参考[官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#django.db.models.ManyToManyField.db_table)
{.is-info}


# 增

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

# 删

## delete()

对 QuerySet 中的所有行执行 SQL 删除查询，并返回删除的对象数量和每个对象类型的删除数量的字典。

```python
>>> b = Blog.objects.get(pk=1)

# Delete all the entries belonging to this Blog.
>>> Entry.objects.filter(blog=b).delete()
(4, {'weblog.Entry': 2, 'weblog.Entry_authors': 2})
```

> 默认情况下，Django 的 ForeignKey 模拟了 SQL 约束 `ON DELETE CASCADE`
{.is-info}

delete() 方法进行批量删除，会为所有被删除的对象（包括级联删除）发出 `pre_delete` 和 `post_delete` 信号。

Django 需要将对象获取到内存中来发送信号和处理级联。但是，如果没有级联和信号，那么 Django 可能会采取**快速路径**删除对象，而不需要将其获取到内存中。对于大面积的删除，这可以使内存使用量大大降低。也可以减少执行查询的数量。

设置为 `on_delete=DO_NOTHING` 的外键不会阻止在删除时采取快速路径。

# 改

## update_or_create()

用给定的 kwargs 更新对象的一种方便方法，是必要时创建一个新对象。defaults 是用来更新对象的`(field, value)`对的字典。defaults 中的值可以是可调用对象。

具体的含义与注意事项与 `get_or_update()` 一样。

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

上面的例子可以使用 `update_or_create()` 重写

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

# QuerySet API


## 查询返回 QuerySet 的方法

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

### exclude()

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

### order_by() 和 reverse()

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

### values() 和 values_list()

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

### date() 和 datetimes()

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

### union() 和 intersection() 和 difference()

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

### select_related() 和 prefetch_related()

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

## 查询不返回 QuerySet 的方法

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


### in_bulk()

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

### iterator()

查询直接读取结果不在 QuerySet 级别做任何缓存，返回一个迭代器。对于一个只需要访问一次就能返回大量对象的 QuerySet 来说，这可以带来更好的性能，并显著减少内存。

> QuerySet 通常会在内部缓存其结果，默认的迭代器调用 iterator() 并缓存返回值。
{.is-info}

chunk_size 参数控制 Django 从数据库驱动中获取的批次大小。批量越大，就会减少与数据库驱动通信的开销，但代价是略微增加内存消耗。默认值 2000。

> 使用 iterator() 会导致 refetch_related() 调用被忽略，因为这两种优化方式放在一起没有意义。
{.is-warning}

# 查询条件

> 详细信息参考[官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/querysets/#field-lookups)
{.is-success}

## exact

使用精确的`=`进行查找。如果提供的是一个 None，那么在 SQL 层面就是被解释为 NULL。示例代码如下：

```python
article = Article.objects.get(id__exact=14)
article = Article.objects.get(id__exact=None)
```

以上的两个查找在翻译为SQL语句为如下：

```sql
select ... from article where id=14;
select ... from article where id IS NULL;
```

## iexact

使用 like 进行查找。示例代码如下：

```python
article = Article.objects.filter(title__iexact='hello world')
```

那么以上的查询就等价于以下的SQL语句：

```sql
select ... from article where title like 'hello world';
```

> 注意上面这个 sql 语句，因为在 MySQL 中，没有一个叫做 ilike 的。所以 exact 和 iexact 的区别实际上就是 `LIKE` 和 `=` 的区别，在大部分 `collation=utf8_general_ci` 情况下都是一样的（collation 是用来对字符串比较的）。
{.is-warning}

## contains

大小写敏感，判断某个字段是否包含了某个数据。示例代码如下：

```python
articles = Article.objects.filter(title__contains='hello')
```

在翻译成 SQL 语句为如下：

```sql
select ... where title like binary '%hello%';
```

> 要注意的是，在使用 contains 的时候，翻译成的 sql 语句左右两边是有百分号的，意味着使用的是模糊查询。而 exact 翻译成 sql 语句左右两边是没有百分号的，意味着使用的是精确的查询。
{.is-warning}


## icontains

大小写不敏感的匹配查询。示例代码如下：

```python
articles = Article.objects.filter(title__icontains='hello')
```

在翻译成SQL语句为如下：

```sql
select ... where title like '%hello%';
```

## startswith

判断某个字段的值是否是以某个值开始的。大小写敏感。示例代码如下：

```python
articles = Article.objects.filter(title__startswith='hello')
```

以上代码的意思是提取所有标题以 hello 字符串开头的文章。将翻译成以下 SQL 语句：

```sql
select ... where title like 'hello%';
```

## istartswith

类似于 startswith，但是大小写是不敏感的。

## endswith

判断某个字段的值是否以某个值结束。大小写敏感。示例代码如下：

```python
articles = Article.objects.filter(title__endswith='world')
```

以上代码的意思是提取所有标题以 world 结尾的文章。将翻译成以下 SQL 语句：

```sql
select ... where title like '%world';
```

## iendswith

类似于 endswith，只不过大小写不敏感。

## in

提取那些给定的 field 的值是否在给定的容器中。容器可以为 list、tuple 或者任何一个可以迭代的对象，包括 QuerySet 对象。示例代码如下：

```python
articles = Article.objects.filter(id__in=[1,2,3])
```

以上代码在翻译成 SQL 语句为如下：

```sql
select ... where id in (1,3,4)
```

当然也可以传递一个 QuerySet 对象进去。示例代码如下：

```python
inner_qs = Article.objects.filter(title__contains='hello')
categories = Category.objects.filter(article__in=inner_qs)
```

以上代码的意思是获取那些文章标题包含 hello 的所有分类。将翻译成以下 SQL 语句，示例代码如下：

```sql
select ...from category where article.id in (select id from article where title like '%hello%');
```

## range

判断某个 field 的值是否在给定的区间中。示例代码如下：

```python
from django.utils.timezone import make_aware
from datetime import datetime
start_date = make_aware(datetime(year=2018,month=1,day=1))
end_date = make_aware(datetime(year=2018,month=3,day=29,hour=16))
articles = Article.objects.filter(pub_date__range=(start_date,end_date))
```

以上代码的意思是提取所有发布时间在2018/1/1到2018/12/12之间的文章。将翻译成以下的 SQL 语句：

```sql
select ... from article where pub_time between '2018-01-01' and '2018-12-12'。
```

需要注意的是，以上提取数据，不会包含最后一个值。也就是不会包含2018/12/12的文章。而且另外一个重点，因为我们在 `settings.py`中指定了 `USE_TZ=True`，并且设置了`TIME_ZONE='Asia/Shanghai'`，因此我们在提取数据的时候要使用 `django.utils.timezone.make_aware` 先将 `datetime.datetime` 从 `navie` 时间转换为 `aware` 时间。`make_aware` 会将指定的时间转换为 `TIME_ZONE` 中指定的时区的时间。

## gt

某个 field 的值要大于给定的值。示例代码如下：

```python
articles = Article.objects.filter(id__gt=4)
```

以上代码的意思是将所有 id 大于 4 的文章全部都找出来。将翻译成以下 SQL 语句：

```sql
select ... where id > 4;
```

## gte
类似于 gt，是大于等于。

## lt

类似于 gt 是小于。

## lte

类似于 lt，是小于等于。

## date

针对某些 date 或者 datetime 类型的字段。可以指定 date 的范围。并且这个时间过滤，还可以使用链式调用。示例代码如下：

```python
articles = Article.objects.filter(pub_date__date=date(2018,3,29))
```

以上代码的意思是查找时间为2018/3/29这一天发表的所有文章。将翻译成以下的 sql 语句：

```sql
select ... WHERE DATE(CONVERT_TZ(`front_article`.`pub_date`, 'UTC', 'Asia/Shanghai')) = 2018-03-29
```

## year

根据年份进行查找。示例代码如下：

```python
articles = Article.objects.filter(pub_date__year=2018)
articles = Article.objects.filter(pub_date__year__gte=2017)
```

以上的代码在翻译成 SQL 语句为如下：

```sql
select ... where pub_date between '2018-01-01' and '2018-12-31';
select ... where pub_date >= '2017-01-01';
```

## iso_year

对于日期和日期时间字段，精确的 ISO 8601 周号年份匹配

```python
Entry.objects.filter(pub_date__iso_year=2005)
Entry.objects.filter(pub_date__iso_year__gte=2005)
```

## month

对于日期和日期时间字段，精确的月份匹配

```python
Entry.objects.filter(pub_date__month=12)
Entry.objects.filter(pub_date__month__gte=6)
```

SQL 等价于：

```sql
SELECT ... WHERE EXTRACT('month' FROM pub_date) = '12';
SELECT ... WHERE EXTRACT('month' FROM pub_date) >= '6';
```

## day

对于日期和日期时间字段，精确匹配日期

举例：

```python
Entry.objects.filter(pub_date__day=3)
Entry.objects.filter(pub_date__day__gte=3)
```

SQL 等价于：

```sql
SELECT ... WHERE EXTRACT('day' FROM pub_date) = '3';
SELECT ... WHERE EXTRACT('day' FROM pub_date) >= '3';
```

## week

对于日期和日期时间字段，根据 ISO-8601 ，返回星期号（1-52 或 53），即星期从星期一开始，第一周包含一年的第一个星期四。

```python
Entry.objects.filter(pub_date__week=52)
Entry.objects.filter(pub_date__week__gte=32, pub_date__week__lte=38)
```

## week_day

对于日期和日期时间字段，“星期几”匹配

```python
Entry.objects.filter(pub_date__week_day=2)
Entry.objects.filter(pub_date__week_day__gte=2)
```

## iso_week_day

对于日期和日期时间字段，精确匹配 ISO 8601 星期几

```python
Entry.objects.filter(pub_date__iso_week_day=1)
Entry.objects.filter(pub_date__iso_week_day__gte=1)
```

## quarter

对于日期和日期时间字段，取 1 到 4 之间的整数值，代表一年中的季度。

```python
Entry.objects.filter(pub_date__quarter=2)
```

## time

对于日期时间字段，将其值强制转换为时间

```python
Entry.objects.filter(pub_date__time=datetime.time(14, 30))
Entry.objects.filter(pub_date__time__range=(datetime.time(8), datetime.time(17)))
```

## hour

对于日期时间和时间字段，精确的小时匹配

```python
Event.objects.filter(timestamp__hour=23)
Event.objects.filter(time__hour=5)
Event.objects.filter(timestamp__hour__gte=12)
```

SQL 等价于：

```sql
SELECT ... WHERE EXTRACT('hour' FROM timestamp) = '23';
SELECT ... WHERE EXTRACT('hour' FROM time) = '5';
SELECT ... WHERE EXTRACT('hour' FROM timestamp) >= '12';
```

## minute

对于日期时间和时间字段，精确的分钟匹配。取 0 到 59 之间的整数

```python
Event.objects.filter(timestamp__minute=29)
Event.objects.filter(time__minute=46)
Event.objects.filter(timestamp__minute__gte=29)
```

SQL 等价于：

```sql
SELECT ... WHERE EXTRACT('minute' FROM timestamp) = '29';
SELECT ... WHERE EXTRACT('minute' FROM time) = '46';
SELECT ... WHERE EXTRACT('minute' FROM timestamp) >= '29';
```

## second

对于日期时间和时间字段，精确的秒匹配。取 0 到 59 之间的整数。

```python
Event.objects.filter(timestamp__second=31)
Event.objects.filter(time__second=2)
Event.objects.filter(timestamp__second__gte=31)
```

SQL 等价于：

```sql
SELECT ... WHERE EXTRACT('second' FROM timestamp) = '31';
SELECT ... WHERE EXTRACT('second' FROM time) = '2';
SELECT ... WHERE EXTRACT('second' FROM timestamp) >= '31';
```


## regex 和 iregex

大小写敏感和大小写不敏感的正则表达式。示例代码如下：

```python
articles = Article.objects.filter(title__regex=r'^hello')
```

以上代码的意思是提取所有标题以 hello 字符串开头的文章。将翻译成以下的 SQL 语句：

```sql
select ... where title regexp binary '^hello';
```

iregex 是大小写不敏感的。

# 聚合函数

| 函数名     | 描述                           |
| ---------- | ------------------------------ |
| `Avg`      | 返回表达式的平均值。           |
| `Count`    | 计算返回的对象数               |
| `Max`      | 返回表达式的最大值。           |
| `Min`      | 返回表达式的最小值。           |
| `StdDev`   | 返回给定表达式中数据的标准差。 |
| `Sum`      | 返回表达式中所有值的总和。     |
| `Variance` | 返回给定表达式中数据的方差。   |

聚合函数是通过 aggregate 方法来实现的。

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

# Count 类中，还有另外一个参数叫做 distinct，默认是等于 False，如果是等于 True，那么将去掉那些重复的值
>>> Book.objects.all().aggregate(Count('price',distinct=True))

### Sum()
>>> Book.objects.aggregate(Sum('bread'))
{'bread__sum': 126}
```

`aggregate()` 函数返回值为一个字典，默认字典的键是根据字段名和聚合函数而自动生成的（`字段名_聚合函数`）也可以指定的名称。

```python
>>> Book.objects.aggregate(average_price=Avg('price'))
{'average_price': 34.35}
```

如果你想生成更多的聚合内容，你需要在 `aggregate()` 子句中加入其它参数即可

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

## aggregate 和 annotate 的区别

1. aggregate：返回使用聚合函数后的字段和值。
2. annotate：在原来模型字段的基础之上添加一个使用了聚合函数的字段，并且在使用聚合函数的时候，会使用当前这个模型的主键进行分组（group by）

比如以上 Sum 的例子，如果使用的是 annotate，那么将在每条图书的数据上都添加一个字段叫做 bread，计算这本书的阅读总量。而如果使用的是 aggregate ，那么将求所有图书的阅读总量。


> [查询表达式官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/expressions/#f-expressions)
{.is-success}

# F() 表达式

有时需要在一个字段上执行一个简单的算术任务，比如递增或递减当前值。一种方法是在 Python 中进行运算，比如：

```python
reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed += 1
reporter.save()
```

这一过程是从数据库中提取了 `reporter.stories_filed` 的值到内存中，使用 Python 操作符对其进行操作，然后将对象保存回数据库

然而使用 F() 表达式操作方法如下：

```python
from django.db.models import F

reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed = F('stories_filed') + 1
reporter.save()
```

看似是一个普通的 Python 赋值给一个实例属性 ，实际当 Django 遇到 F() 表达式的实例时，它会覆盖标准的 Python 运算符来创建一个封装的 SQL 表达式，在数据库层面描述所需的操作。Python 层面并不知道 `reporter.stories_filed` 的值是多少。

要访问这样保存的新值，必须重新加载对象：

```python
reporter = Reporters.objects.get(pk=reporter.pk)
# Or, more succinctly:
reporter.refresh_from_db()
```

F() 表达式还可以与 `update()` 一起用于对象实例的 QuerySets，从而把上面使用的两个查询 `get()` 和 `save()` 减少到只有一个：

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

## 使用 F() 表达式避免多线程竞争

如果两个 Python 线程执行下面的代码，一个线程可以在另一个线程从数据库中获取一个字段的值后，检索、递增并保存它。第二个线程保存的值将基于原始值，从而导致第一个线程的工作并不是基于第二个线程只有的结果。

```python
reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed += 1
reporter.save()
```

如果数据库负责更新字段，那么这个过程就比较稳健：它只会在执行 `save()` 或 `update()` 时，根据数据库中字段的值来更新字段，而不是根据检索实例时的值来更新。

## F() 表达式赋值在 Model.save() 之后持续存在

例如：

```reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed = F('stories_filed') + 1
reporter.save()

reporter.name = 'Tintin Jr.'
reporter.save()python
```

在这种情况下，`stories_filed` 将被更新两次。如果最初是 1，最终值将是 3。这种持久性可以通过在保存模型对象后重新加载来避免，例如，使用 `refresh_from_db()`。

## 在过滤器中使用 F() 表达式

Django 中 F 表达式的实例充当查询中的模型字段的引用。这些引用可在查询过滤器中用于在同一模型实例中比较两个不同的字段。

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

F() 表达式通过 `.bitand()`， `.bitor()`， `.bitxor()`，`.bitrightshift()` 和 `.bitleftshift()` 支持位操作。例子:

```python
F('somefield').bitand(16)
```

> 更多用法，请参考[官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/expressions/#using-f-with-annotations)
{.is-info}

# Q() 对象

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
