# ReporteIngresosMusi
## Reporte de Ingresos Musimundo

> 3.12
El reporte de “Ingresos” (lo que ya está armado) que muestre solamente los ingresos de mercadería del proveedor. Solo lo que se compró y el proveedor entregó. Como dijimos, valuado al día del ingreso.
Es decir, que no considere otros movimientos de mercadería. 

Estaríamos hablando de los movimientos 101 y 102. El problema que supongo vamos a tener acá es con los movimientos 311. Estos se usan para varias cosas. Entre ellas “anular” un ingreso. ¿No? Sería imposible saber qué movimiento anulo ingreso y cual otro, por ejemplo, “recuperó” mercadería de otro almacén.

---

Pasos
1. IngresosEgresosTMP = Importar tablas MKPF - MSEG - EKPO - EKKO - MARA - LFA1
    <t><n>MKPF</n><a>T00001</a><td>Cabecera de doc.artículo</td><tt>1</tt><fs><f>MBLNR</f><f>MJAHR</f><f>VGART</f><f>BLDAT</f><f>BUDAT</f><f>CPUDT</f><f>USNAM</f><f>XBLNR</f><f>LE_VBELN</f></fs>
    <wc>
    <w><f>CPUDT</f><o>6</o><l>%v_Desde%</l><h>%v_Hasta%</h></w>
    <w><f>VGART</f><o>0</o><l>WE</l><h></h></w>
    </wc></t>

    <t><n>MSEG</n><a>T00002</a><td>Segmento doc.material</td><tt>1</tt><fs><f>MBLNR</f><f>MJAHR</f><f>ZEILE</f><f>LINE_ID</f><f>BWART</f><f>MATNR</f><f>WERKS</f><f>LGORT</f><f>LIFNR</f><f>DMBTR</f><f>MENGE</f><f>EBELN</f><f>EBELP</f><f>GJAHR</f><f>BUKRS</f><f>BSTMG</f></fs>
    <wc>
    <w><f>BWART</f><o>0</o><l>101</l><h></h></w>
    <w><f>BWART</f><o>0</o><l>102</l><h></h></w>
    <w><f>WERKS</f><o>7</o><l>M*</l><h></h></w>
    </wc></t>

    <t><n>EKPO</n><a>T00004</a><td>Posición del documento de compras</td><tt>1</tt><fs><f>EBELN</f><f>EBELP</f><f>MATNR</f><f>TXZ01</f><f>WERKS</f><f>MENGE</f><f>MTART</f></fs><wc></wc></t>

    <t><n>EKKO</n><a>T00005</a><td>Cabecera del documento de compras</td><tt>1</tt><fs><f>EBELN</f><f>BUKRS</f><f>BSTYP</f><f>BSART</f><f>AEDAT</f><f>LIFNR</f></fs><wc></wc></t>

    <t><n>MARA</n><a>T00007</a><td>Datos generales artículo</td><tt>1</tt><fs><f>MATNR</f><f>MTART</f><f>BRAND_ID</f><f>MATKL</f><f>VOLUM</f></fs><wc></wc></t>
    
    <t><n>LFA1</n><a>T00011</a><td>Maestro de proveedores (parte general)</td><tt>1</tt><fs><f>LIFNR</f><f>NAME1</f></fs><wc></wc></t>
    
    <jc><pt><pa>T00001</pa><pf>MBLNR</pf></pt><ct><ca>T00002</ca><cf>MBLNR</cf></ct></jc>
    <jc><pt><pa>T00001</pa><pf>MJAHR</pf></pt><ct><ca>T00002</ca><cf>MJAHR</cf></ct></jc>
    <jc><pt><pa>T00002</pa><pf>EBELN</pf></pt><ct><ca>T00004</ca><cf>EBELN</cf></ct></jc>
    <jc><pt><pa>T00002</pa><pf>EBELP</pf></pt><ct><ca>T00004</ca><cf>EBELP</cf></ct></jc>
    <jc><pt><pa>T00004</pa><pf>EBELN</pf></pt><ct><ca>T00005</ca><cf>EBELN</cf></ct></jc>
    <jc><pt><pa>T00004</pa><pf>MATNR</pf></pt><ct><ca>T00007</ca><cf>MATNR</cf></ct></jc>
    <jc><pt><pa>T00005</pa><pf>LIFNR</pf></pt><ct><ca>T00011</ca><cf>LIFNR</cf></ct></jc>

2. GruposArticulo = Importar tablas KLAH - SWOR
    <t><n>KLAH</n><a>T00001</a><td>Datos cabecera clase</td><tt>1</tt><fs><f>CLINT</f><f>KLART</f><f>CLASS</f><f>STATU</f></fs><wc><w><f>BISDT</f><o>0</o><l>99991231</l><h></h></w><w><f>KLART</f><o>0</o><l>026</l><h></h></w></wc></t>
    
    <t><n>SWOR</n><a>T00002</a><td>Sistema de clasificación: palabras clave</td><tt>1</tt><fs><f>CLINT</f><f>KSCHL</f><f>KSCHG</f></fs><wc><w><f>SPRAS</f><o>0</o><l>S</l><h></h></w></wc></t>
    <jc><pt><pa>T00001</pa><pf>CLINT</pf></pt><ct><ca>T00002</ca><cf>CLINT</cf></ct></jc>

3. Marcas = Importar tabla WRF_BRANDS_T
    <t><n>WRF_BRANDS_T</n><a>T00001</a><td>Nombres de marcas</td><tt>1</tt><fs><f>BRAND_ID</f><f>BRAND_DESCR</f></fs><wc><w><f>LANGUAGE</f><o>0</o><l>S</l><h></h></w></wc></t>

4. CostoTMP = Importar tablas A073 - KONP
    <t><n>A073</n><a>T00001</a><td>Artículo por OrgVta/CanDistr</td><fs><f>KAPPL</f><f>KSCHL</f><f>MATNR</f><f>KNUMH</f></fs>
    <w><f>KAPPL</f><o>0</o><l>V</l><h></h></w>
    <w><f>KSCHL</f><o>0</o><l>ZKP0</l><h></h></w>
    <w><f>VKORG</f><o>0</o><l>1000</l><h></h></w>
    <w><f>VTWEG</f><o>0</o><l>20</l><h></h></w>
    <w><f>DATBI</f><o>5</o><l>%v_Desde%</l><h></h></w>
    </t>

    <t><n>KONP</n><a>T00002</a><td>Condiciones (Posición)</td><fs><f>KBETR</f></fs><wc></wc></t>
    
    <jc><pt><pa>T00001</pa><pf>KAPPL</pf></pt><ct><ca>T00002</ca><cf>KAPPL</cf></ct></jc>
    <jc><pt><pa>T00001</pa><pf>KSCHL</pf></pt><ct><ca>T00002</ca><cf>KSCHL</cf></ct></jc>
    <jc><pt><pa>T00001</pa><pf>KNUMH</pf></pt><ct><ca>T00002</ca><cf>KNUMH</cf></ct></jc>

5.  VentaTMP = Importar tablas A073 - KONP     
    <t><n>A073</n><a>T00001</a><td>Artículo por OrgVta/CanDistr</td><fs><f>KAPPL</f><f>KSCHL</f><f>MATNR</f><f>KNUMH</f></fs>
    <w><f>KAPPL</f><o>0</o><l>V</l><h></h></w>
    <w><f>KSCHL</f><o>0</o><l>VKP0</l><h></h></w>
    <w><f>VKORG</f><o>0</o><l>1000</l><h></h></w>
    <w><f>VTWEG</f><o>0</o><l>20</l><h></h></w>
    <w><f>DATBI</f><o>5</o><l>%v_Desde%</l><h></h></w>
    </t>

    <t><n>KONP</n><a>T00002</a><td>Condiciones (Posición)</td><fs><f>KBETR</f></fs><wc></wc></t>
    
    <jc><pt><pa>T00001</pa><pf>KAPPL</pf></pt><ct><ca>T00002</ca><cf>KAPPL</cf></ct></jc>
    <jc><pt><pa>T00001</pa><pf>KSCHL</pf></pt><ct><ca>T00002</ca><cf>KSCHL</cf></ct></jc>
    <jc><pt><pa>T00001</pa><pf>KNUMH</pf></pt><ct><ca>T00002</ca><cf>KNUMH</cf></ct></jc>    

6.  ProveedorRegular = Importar tablas EINA - MARA - LFA1
    <t><n>EINA</n><a>T00001</a><td>Registro info de compras: Datos generales</td><tt>1</tt><fs><f>INFNR</f><f>MATNR</f><f>LIFNR</f><f>RELIF</f></fs>
    <w><f>RELIF</f><o>0</o><l>X</l><h></h></w></wc></t>

    <t><n>MARA</n><a>T00002</a><td>Datos generales artículo</td><tt>1</tt><fs><f>MATNR</f><f>MTART</f></fs>
    <w><f>MTART</f><o>0</o><l>ZPRO</l><h></h></w>
    <w><f>MTART</f><o>0</o><l>ZBLA</l><h></h></w>
    <w><f>MTART</f><o>0</o><l>ZPVV</l><h></h></w></wc></t>

    <t><n>LFA1</n><a>T00003</a><td>Maestro de proveedores (parte general)</td><tt>1</tt><fs><f>LIFNR</f><f>NAME1</f></fs><wc></wc></t></ts>
    
    <jc><pt><pa>T00001</pa><pf>MATNR</pf></pt><ct><ca>T00002</ca><cf>MATNR</cf></ct></jc>
    <jc><pt><pa>T00001</pa><pf>LIFNR</pf></pt><ct><ca>T00003</ca><cf>LIFNR</cf></ct></jc>

