# AsistencIAU - CloudFormation anidado

## Estructura
- `anidados/principal.yaml`: coordina red, seguridad y los dos servidores.
- `anidados/red.yaml`: VPC, subred publica, gateway y rutas.
- `anidados/seguridad.yaml`: firewall frontend/backend.
- `anidados/servidor.yaml`: plantilla EC2 reutilizada dos veces.
- `anidados/seguridad.guard`: reglas de comprobacion opcionales.

## Despliegue en AWS (PowerShell)

1. Crear un bucket S3 unico en `us-east-1` (por consola o CLI). El bucket puede generar costos.
2. Desde la raiz del proyecto, subir las plantillas secundarias:

```powershell
aws s3 sync .\cloudformation\anidados\ s3://NOMBRE-BUCKET/cloudformation/anidados/ --exclude "principal.yaml" --exclude "*.guard" --profile asistencia --region us-east-1
```

3. En AWS CloudFormation > Crear pila > Cargar un archivo de plantilla, seleccionar `cloudformation/anidados/principal.yaml`.
4. Completar `TemplatesBucket`, `SshCidr` (IP publica /32), `LlavePublica` (contenido del archivo .pub) y los demas parametros. La pila necesita permisos para crear stacks anidados, EC2, VPC y KeyPair.
5. Confirmar el despliegue y consultar las salidas `IpFrontend`, `IpBackend`, `SshFrontend`, `SshBackend`.

**Importante:** las dos EC2 son publicas como en el archivo original. Para produccion, el backend debe ser privado y sus puertos web no deben estar abiertos al mundo. Este proyecto crea infraestructura, no instala automaticamente Apache, PHP o PostgreSQL. Los servicios AWS (EC2, EBS, IPv4 publicas y S3) pueden generar cargos. `seguridad.guard` no se ejecuta automaticamente. La clave publica es sensible para el acceso SSH, pero no es una clave privada; nunca subas la privada.
