// This file handles all the user input and updates to the collision calculations. The user can use the arrow key 
// and select other keys (see README) to adjust the size of the two spheres, their distance apart, and the angle
// of motion of the moving sphere. The direction of movement of the static sphere is indicated by the movementCylinder,
// the point of collision is indicated by the collisionPoint, and the location of the moving sphere upon collision
// is indicated by the collisionSphere. The distance between the two sphere and the distance the moving sphere
// must travel before collision are indicate by Text objects placed in the upper left corner. Program does not currently
// provide functionality for the user to adjust the default viewpoint. 

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class CollisionScript : MonoBehaviour {

// === FIELDS ==============================================================================================

    // Object transforms

    public Transform staticSphere;
    public Transform sweptSphere;
    public Transform collisionPoint;
    public Transform collisionSphere;
    public Transform movementCylinder;

    // Text displaying distance between spheres
    public Text seperationText;
    public string SEP_STRING;

    // Text displaying distance to collision
    public Text distanceText;
    public string DIST_STRING;
    public string N_A;              // Not applicable (when no collision occurs)

    // Movement / rotation constants
    public float ANG_ROT_SPD;   // Angular rotation in deg per update
    public float SCALE_SPEED;   // Change in scale per update
    public float DISP_SPEED;    // Displacement per update
    public float MIN_DIAMETER;    // Minimum diameter for spheres
    public float CYL_CONST;       

    public bool collision;      // Indicates if collision occurs

// === UPDATE ==============================================================================================

    // Handle user inputs and update object transform accordingly
    private void Update () {

        // Adjust angle of movement  
        if (Input.GetKey(KeyCode.RightArrow)) { rightArrow(); }
        if (Input.GetKey(KeyCode.LeftArrow)) { leftArrow(); }
        if (Input.GetKey(KeyCode.UpArrow)) { upArrow(); }
        if (Input.GetKey(KeyCode.DownArrow)) { downArrow(); }

        // Adjust size of moving sphere
        if (Input.GetKey(KeyCode.Q)) { qKey(); }
        if (Input.GetKey(KeyCode.A)) { aKey(); }

        // Adjust size of static sphere
        if (Input.GetKey(KeyCode.W)) { wKey(); }
        if (Input.GetKey(KeyCode.S)) { sKey(); }

        // Adjust position of static sphere
        if (Input.GetKey(KeyCode.E)) { eKey(); }
        if (Input.GetKey(KeyCode.D)) { dKey(); }

        // Prevent spheres from overlapping, update position of collision point 
        // and movement cylinder, and update text displays
        preventOverlap();
        updateCylinder();
        updateCollision();
        updateText();
    }

// === HANDLE USER INPUT ==============================================================================================

    // Functions to adjust angle of movement of sphere
    // Right / left arrow keys rotate counter-clockwise / clockwise (respectively) about global vertical (y) axis
    // Up / down arrow keys rotate clockwise / counter-clockwise (respectively) about global right (z) axis
   
    void rightArrow() { sweptSphere.Rotate(Vector3.up * ANG_ROT_SPD); }

    void leftArrow() { sweptSphere.Rotate(Vector3.up * -ANG_ROT_SPD); } 

    void upArrow() { sweptSphere.Rotate(Vector3.right * -ANG_ROT_SPD); }

    void downArrow() { sweptSphere.Rotate(Vector3.right * ANG_ROT_SPD); }

    // Functions to control size of the swept sphere
    // Q-key increases size, A-key decreases it; ensure sphere no smaller than MIN_DIAMETER

    void qKey()
    {
        sweptSphere.localScale += Vector3.one * SCALE_SPEED;
        collisionSphere.localScale += Vector3.one * SCALE_SPEED;
    }

    void aKey()
    {
        Vector3 ls = sweptSphere.localScale;
        ls -= Vector3.one * SCALE_SPEED;

        if (ls.x >= MIN_DIAMETER && ls.y >= MIN_DIAMETER && ls.z >= MIN_DIAMETER)
        {
            sweptSphere.localScale = ls;
            collisionSphere.localScale -= Vector3.one * SCALE_SPEED;
        }
        else
        {
            sweptSphere.localScale = Vector3.one * MIN_DIAMETER;
            collisionSphere.localScale = Vector3.one * MIN_DIAMETER;
        }
    }

    // Functions to control the size of the static sphere
    // W-key increases size, S-key decreases it; do not allow size smaller than MIN_DIAMETER

    void wKey()
    {
        staticSphere.localScale += Vector3.one * SCALE_SPEED;
    }

    void sKey()
    {
        Vector3 ls = staticSphere.localScale;
        ls -= Vector3.one * SCALE_SPEED;

        if (ls.x >= MIN_DIAMETER || ls.y >= MIN_DIAMETER || ls.z >= MIN_DIAMETER)
        {
            staticSphere.localScale = ls;
        }
        else
        {
            staticSphere.localScale = Vector3.one * MIN_DIAMETER;
        }
    }

    // Functions to adjust the position of the stationary sphere relative to the moving sphere
    // E-key moves it away from the swept sphere (along z axis); D-key moves it towards

    void eKey() { staticSphere.Translate(Vector3.forward * DISP_SPEED); }

    void dKey() { staticSphere.Translate(Vector3.back * DISP_SPEED); }

    // Updates the two text displays
    void updateText()
    {
        // Seperation text
        Vector3 between = staticSphere.position - sweptSphere.position;
        float distanceBetween = between.magnitude;
        seperationText.text = SEP_STRING + System.Math.Round(distanceBetween, 2).ToString();

        // Distance text
        if(collision)
        {
            Vector3 travel = collisionSphere.position - sweptSphere.position;
            float travelDistance = travel.magnitude;
            distanceText.text = DIST_STRING + System.Math.Round(travelDistance,2).ToString();
        }
        else
        {
            distanceText.text = DIST_STRING + N_A;
        }
    }

    // Positions the cylinder. It should start at the swept sphere, point in its direction of movement, have equal diameter, and 
    // have length CYL_CONST units longer than the distance between the centers of the two spheres
    void updateCylinder()
    {
        // Cylinder scale
        float distanceBetweenSpheres = staticSphere.position.z;
        float sweptSphereDiam = sweptSphere.localScale.x;
        movementCylinder.localScale = new Vector3(sweptSphereDiam, (distanceBetweenSpheres + CYL_CONST)/2, sweptSphereDiam);

        // Cylinder rotation
        Vector3 direction = sweptSphere.forward;
        movementCylinder.rotation = Quaternion.LookRotation(sweptSphere.forward);
        movementCylinder.Rotate(new Vector3(90, 0, 0));

        // Cylinder position
        movementCylinder.position = direction * (distanceBetweenSpheres + CYL_CONST) / 2;      
    }


    // Prevents the two sphere's initial positions from overlapping (they can touch, but not overlap)
    // (based on assumption that sphere should not overlap)
    void preventOverlap()
    {
        Vector3 between = staticSphere.position - sweptSphere.position;
        float distanceBetween = between.magnitude;
        float minDistanceBetween = (staticSphere.localScale.x / 2) + (sweptSphere.localScale.x / 2);

        if (distanceBetween < minDistanceBetween)
        {
            staticSphere.position = Vector3.forward * minDistanceBetween;
        }
    }


// === UPDATE COLLISION ====================================================================================

    // Update the position of the collision sphere (indicates where moving sphere would be
    // when it collides with stationary sphere) and collision point (indicates where moving sphere would be when collision occurs)
    void updateCollision()
    {
        // Get vectors (positions and directions) and radii
        Vector3 sweptOrig = sweptSphere.position;
        Vector3 sweptDir = sweptSphere.forward;
        float sweptRad = sweptSphere.localScale.x / 2;
        Vector3 staticOrig = staticSphere.position;
        float staticRad = staticSphere.localScale.x /2;

        // Collision equation
        float a = 1;                                        // Movement direction is unit vector
        Vector3 betweenOrigs = sweptOrig - staticOrig;
        float b = 2 * Vector3.Dot(sweptDir, betweenOrigs);
        float c = betweenOrigs.sqrMagnitude - Mathf.Pow(sweptRad + staticRad, 2.0F);

        QuadEqn qe = new QuadEqn(a, b, c);

        if (!qe.hasSoln() || qe.smlSoln() < 0 )
        {
            // If spheres will not collide, make collision point and collision sphere invisible;
            // update distance between text
            collisionPoint.gameObject.GetComponent<Renderer>().enabled = false;
            collisionSphere.gameObject.GetComponent<Renderer>().enabled = false;

            collision = false;
        }
        else
        {
            // Turn on visibility of collision point and collision sphere
            collisionPoint.gameObject.GetComponent<Renderer>().enabled = true;
            collisionSphere.gameObject.GetComponent<Renderer>().enabled = true;

            // Move collision point and collision sphere to appropriate spots
            float distance = qe.smlSoln();
            Vector3 collisionSphereCtr = sweptOrig + distance * sweptDir;
            collisionSphere.position = collisionSphereCtr;

            Vector3 collCtrtoStatOrig = staticOrig - collisionSphereCtr;
            Vector3 collPoint = collisionSphereCtr + collCtrtoStatOrig.normalized * sweptRad;
            collisionPoint.position = collPoint;

            collision = true;
        }

    }
 
    // Represents a quadratic equation of the form a*(x^2) + b*x + c = 0
    public class QuadEqn
    {
        float a;
        float b;
        float c;

        public QuadEqn(float a, float b, float c)
        {
            this.a = a; this.b = b; this.c = c;
        }

        // Return true if quadratic equation has a real solution
        public bool hasSoln()
        {
            return Mathf.Pow(b, 2.0F) - 4 * a * c >= 0;
        }

        // Return smaller root (we only require smaller root for this problem)
        // Requires: hasSoln is true
        public float smlSoln()
        {
            return (-b - Mathf.Sqrt(Mathf.Pow(b, 2.0F) - 4 * a * c)) / (2 * a);
        }
    }
}
