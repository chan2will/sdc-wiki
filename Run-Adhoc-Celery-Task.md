```
ssh <server_host>
docker exec -it vulcan /bin/bash
. /venv/bin/activate
cd app
./manage.py shell_plus
from common.tasks import RefreshSearchMaterializedViewTask
RefreshSearchMaterializedViewTask.delay()
```