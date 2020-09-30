# ControlObjWithCamera
控制物体旋转缩放
        
        
    public class WowMainCamera : MonoBehaviour
   {
        
    public Transform target;

    public float targetHeight = 1.0f;

    public float maxDistance = 100;
    public float minDistance = -50;

    public float xSpeed = 250.0f;
    public float ySpeed = 120.0f;

    public int yMinLimit;
    public int yMaxLimit;

    public int zoomRate = 40;

    public float rotationDampening = 3.0f;
    public float zoomDampening = 10.0f;

    public float x;
    public float y;
    public float currentDistance;
    public float desiredDistance;
    private float correctedDistance;

    private Rect scollRect;
    public bool CanBeControl = true;
    public bool canBeZoom = true;


    void LateUpdate()
    {
        if (CanBeControl)
        {
            if (Input.GetMouseButton(0))
            {
                x += Input.GetAxis("Mouse X") * xSpeed * 0.02f;
                y -= Input.GetAxis("Mouse Y") * ySpeed * 0.02f;
            }
        }

        y = Mathf.Clamp(y, yMinLimit, yMaxLimit);
        x = x % 360;
        y = y % 360;
        Quaternion rotation = Quaternion.Euler(y, x, 0);

        if (canBeZoom)
        {
            desiredDistance -= Input.GetAxis("Mouse ScrollWheel") * Time.deltaTime * zoomRate * 2;
        }
        desiredDistance = Mathf.Clamp(desiredDistance, minDistance, maxDistance);
        correctedDistance = desiredDistance;
        Vector3 position = target.position - (rotation * Vector3.forward * desiredDistance + new Vector3(0, -targetHeight, 0));
        bool isCorrected = false;
       
        currentDistance = !isCorrected || correctedDistance > currentDistance ? Mathf.Lerp(currentDistance, correctedDistance, Time.deltaTime * zoomDampening) : correctedDistance;

        position = target.position - (rotation * Vector3.forward * currentDistance + new Vector3(0, -targetHeight - 0.05f, 0));

        transform.rotation = rotation;
        transform.position = position;
    }

    private static float ClampAngle(float angle, float min, float max)
    {
        if (angle < -360)
            angle += 360;
        if (angle > 360)
            angle -= 360;
        return Mathf.Clamp(angle, min, max);
    }


}
