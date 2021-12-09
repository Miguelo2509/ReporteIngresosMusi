# ReporteIngresosMusi
## Reporte de Ingresos Musimundo

> 3.12
El reporte de “Ingresos” (lo que ya está armado) que muestre solamente los ingresos de mercadería del proveedor. Solo lo que se compró y el proveedor entregó. Como dijimos, valuado al día del ingreso.
Es decir, que no considere otros movimientos de mercadería. 

Estaríamos hablando de los movimientos 101 y 102. El problema que supongo vamos a tener acá es con los movimientos 311. Estos se usan para varias cosas. Entre ellas “anular” un ingreso. ¿No? Sería imposible saber qué movimiento anulo ingreso y cual otro, por ejemplo, “recuperó” mercadería de otro almacén.

---

Pasos
1. IngresosEgresosTMP = Importar tablas MKPF - MSEG - EKPO - EKKO - MARA - LFA1
   # MKPF AS T00001:Cabecera de doc.artículo
    Campos:
        MBLNR MJAHR VGART BLDAT BUDAT CPUDT USNAM XBLNR LE_VBELN
    Filtros:
        CPUDT BETWEEN %v_Desde% %v_Hasta%
        VGART = WE

   # MSEG AS T00002:Segmento doc.material
    Campos:
        MBLNR MJAHR ZEILE LINE_ID BWART MATNR WERKS LGORT LIFNR DMBTR MENGE EBELN EBELP GJAHR BUKRS BSTMG
    Filtros:
        BWART IN (101,102)
        WERKS LIKE M*
    
   # EKPO AS T00004:Posición del documento de compras
    Campos:
        EBELN EBELP MATNR TXZ01 WERKS MENGE MTART

   # EKKO AS T00005:Cabecera del documento de compras
    Campos:
        EBELN BUKRS BSTYP BSART AEDAT LIFNR

   # MARA AS T00007:Datos generales artículo
    Campos:
    MATNR MTART BRAND_ID MATKL VOLUM
    
   # LFA1 AS T00011:Maestro de proveedores (parte general)
    Campos:
        LIFNR NAME1
    
    Uniones:
        T00001.MBLNR=T00002.MBLNR
        T00001.MJAHR=T00002.MJAHR
        T00002.EBELN=T00004.EBELN
        T00002.EBELP=T00004.EBELP
        T00004.EBELN=T00005.EBELN
        T00004.MATNR=T00007.MATNR
        T00005.LIFNR=T00011.LIFNR

2. GruposArticulo = Importar tablas KLAH - SWOR
   # KLAH AS T00001:Datos cabecera clase
    Campos:
        CLINT KLART CLASS STATU
    Filtros:
        BISDT = 99991231
        KLART= 026
    
   # SWOR AS T00002:Sistema de clasificación: palabras clave
    Campos:
        CLINT KSCHL KSCHG
    Filtros:
        SPRAS = S
    
    Uniones:
        T00001.CLINT=T00002.CLINT

3. Marcas = Importar tabla WRF_BRANDS_T
   # WRF_BRANDS_T AS T00001:Nombres de marcas
    Campos:
        BRAND_ID BRAND_DESCR
    Filtros:
        LANGUAGE = S

4. CostoTMP = Importar tablas A073 - KONP - MARA
   # A073 AS T00001:Artículo por OrgVta/CanDistr
    Campos:
        MATNR KNUMH DATAB DATBI
    Filtros:
        KAPPL= V
        KSCHL= ZKP0
        VKORG= 1000
        VTWEG= 20
        DATBI >= %v_Desde%
    
   # KONP AS T00002:Condiciones (Posición)
    Campos:
        KBETR
   # MARA AS T00003: Datos generales artículo
    Campos 
        
    Filtros:
        MTART IN (ZPRO, MTART, ZBLA, ZPVV)

    Uniones:
        T00001.KAPPL=T00002.KAPPL
        T00001.KSCHL=T00002.KSCHL
        T00001.KNUMH=T00002.KNUMH
        T00001.MATNR=T00003.MATNR

5.  VentaTMP = Importar tablas A073 - KONP - MARA     
    # A073 AS T00001:Artículo por OrgVta/CanDistr
    Campos:
        MATNR KNUMH DATAB DATBI
    Filtros:
        KAPPL=V
        KSCHL=VKP0
        VKORG=1000
        VTWEG=20
        DATBI >= %v_Desde%
    
    # KONP AS T00002:Condiciones (Posición)
    Campos:
        KBETR
   # MARA AS T00003: Datos generales artículo
    Campos 
        
    Filtros:
        MTART IN (ZPRO, MTART, ZBLA, ZPVV)

    Uniones:
        T00001.KAPPL=T00002.KAPPL
        T00001.KSCHL=T00002.KSCHL
        T00001.KNUMH=T00002.KNUMH
        T00001.MATNR=T00003.MATNR

6.  ProveedorRegular = Importar tablas EINA - MARA - LFA1
    # EINA AS T00001:Registro info de compras: Datos generales
    Campos:
        INFNR MATNR LIFNR RELIF
    Filtros:
        RELIF=X

    # MARA AS T00002:Datos generales artículo
    Campos::
        MATNR MTART
    Filtros:        
        MTART=ZPRO
        MTART=ZBLA
        MTART=ZPVV

    # LFA1 AS T00003:Maestro de proveedores (parte general)
    Campos:
        LIFNR NAME1

    Uniones:   
        T00001.MATNR=T00002.MATNR
        T00001.LIFNR=T00003.LIFNR

