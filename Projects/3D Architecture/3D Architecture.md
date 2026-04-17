# Lab Project - 3D E-Commerce Platform Architecture on AWS

**AWS Cloud Practitioner Program · Architecture Design Assignment**

---

## What This Project Is

This lab documents the cloud architecture design for a next-generation **3D e-commerce platform** - a web application where users can interact with 3D models of products (furniture, gadgets, fashion items) before purchasing.

The platform is expected to serve **millions of users globally** and must meet five key requirements: high availability, scalability, performance, security, and cost optimization.



---

## Architecture Diagram

[3D E-Commerce Platform-Updated.drawio](https://github.com/user-attachments/files/26662037/3D.E-Commerce.Platform-Updated.drawio)<mxfile host="app.diagrams.net" agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36">
  <diagram name="Страница-1" id="hXvT8Rk896Eu29VOnFGR">
    <mxGraphModel dx="1488" dy="791" grid="1" gridSize="10" guides="1" tooltips="1" connect="1" arrows="1" fold="1" page="1" pageScale="1" pageWidth="827" pageHeight="1169" math="0" shadow="0">
      <root>
        <mxCell id="0" />
        <mxCell id="1" parent="0" />
        <mxCell id="E3uyBDmK6ujrw01T13Bo-59" edge="1" parent="1" source="E3uyBDmK6ujrw01T13Bo-60" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;" target="E3uyBDmK6ujrw01T13Bo-67" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="248" y="59" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-60" parent="1" style="sketch=0;outlineConnect=0;fontColor=#232F3E;gradientColor=none;fillColor=#232F3D;strokeColor=none;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;pointerEvents=1;shape=mxgraph.aws4.users;" value="Users" vertex="1">
          <mxGeometry height="78" width="78" x="90" y="20" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-61" parent="1" style="points=[[0,0],[0.25,0],[0.5,0],[0.75,0],[1,0],[1,0.25],[1,0.5],[1,0.75],[1,1],[0.75,1],[0.5,1],[0.25,1],[0,1],[0,0.75],[0,0.5],[0,0.25]];outlineConnect=0;gradientColor=none;html=1;whiteSpace=wrap;fontSize=12;fontStyle=0;container=1;pointerEvents=0;collapsible=0;recursiveResize=0;shape=mxgraph.aws4.group;grIcon=mxgraph.aws4.group_aws_cloud;strokeColor=#232F3E;fillColor=none;verticalAlign=top;align=left;spacingLeft=30;fontColor=#232F3E;dashed=0;" value="AWS Cloud" vertex="1">
          <mxGeometry height="979" width="1390" x="20" y="120" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-230" parent="E3uyBDmK6ujrw01T13Bo-61" style="fillColor=none;strokeColor=#147EBA;dashed=1;verticalAlign=top;fontStyle=0;fontColor=#147EBA;whiteSpace=wrap;html=1;" value="Availability Zone" vertex="1">
          <mxGeometry height="140" width="371.25" x="860" y="350" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-229" parent="E3uyBDmK6ujrw01T13Bo-61" style="fillColor=none;strokeColor=#147EBA;dashed=1;verticalAlign=top;fontStyle=0;fontColor=#147EBA;whiteSpace=wrap;html=1;" value="Availability Zone" vertex="1">
          <mxGeometry height="280" width="370" x="860" y="520" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-62" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-63" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;" target="E3uyBDmK6ujrw01T13Bo-88" value="">
          <mxGeometry relative="1" as="geometry">
            <Array as="points">
              <mxPoint x="405" y="120" />
              <mxPoint x="219" y="120" />
            </Array>
            <mxPoint x="405" y="178" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-267" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-63" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0;entryY=0.5;entryDx=0;entryDy=0;entryPerimeter=0;strokeColor=light-dark(#000000,#EDEDED);" target="E3uyBDmK6ujrw01T13Bo-64" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="524" y="58.999999999999886" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-269" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-63" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0;entryY=0.25;entryDx=0;entryDy=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-249" value="">
          <mxGeometry relative="1" as="geometry">
            <Array as="points">
              <mxPoint x="405" y="200" />
              <mxPoint x="732" y="200" />
            </Array>
            <mxPoint x="405" y="178" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-63" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#8C4FFF;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.route_53;" value="Route 53" vertex="1">
          <mxGeometry height="78" width="78" x="366" y="20" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-64" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#8C4FFF;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.cloudfront;" value="CloudFront" vertex="1">
          <mxGeometry height="78" width="78" x="620" y="20" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-65" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;outlineConnect=0;fontColor=#232F3E;gradientColor=none;fillColor=#DD344C;strokeColor=none;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;pointerEvents=1;shape=mxgraph.aws4.shield_shield_advanced;" value="AWS Shield" vertex="1">
          <mxGeometry height="78" width="70" x="760" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-66" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-64" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;" target="E3uyBDmK6ujrw01T13Bo-65" value="">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-67" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;outlineConnect=0;fontColor=#232F3E;gradientColor=none;fillColor=#232F3D;strokeColor=none;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;pointerEvents=1;shape=mxgraph.aws4.globe;" value="Internet" vertex="1">
          <mxGeometry height="78" width="78" x="220" y="-40" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-68" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-67" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;" target="E3uyBDmK6ujrw01T13Bo-63" value="">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-69" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-70" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;endArrow=none;endFill=0;startArrow=classic;startFill=1;strokeColor=light-dark(#000000,#EDEDED);" target="E3uyBDmK6ujrw01T13Bo-64" value="">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-205" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-70" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;endArrow=none;endFill=0;startArrow=classic;startFill=1;" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="659" y="690" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-70" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#7AA116;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.s3;" value="S3" vertex="1">
          <mxGeometry height="78" width="78" x="620" y="282" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-71" parent="E3uyBDmK6ujrw01T13Bo-61" style="points=[[0,0],[0.25,0],[0.5,0],[0.75,0],[1,0],[1,0.25],[1,0.5],[1,0.75],[1,1],[0.75,1],[0.5,1],[0.25,1],[0,1],[0,0.75],[0,0.5],[0,0.25]];outlineConnect=0;gradientColor=none;html=1;whiteSpace=wrap;fontSize=12;fontStyle=0;container=1;pointerEvents=0;collapsible=0;recursiveResize=0;shape=mxgraph.aws4.group;grIcon=mxgraph.aws4.group_region;strokeColor=#00A4A6;fillColor=none;verticalAlign=top;align=left;spacingLeft=30;fontColor=#147EBA;dashed=1;" value="Region" vertex="1">
          <mxGeometry height="560" width="562.5" x="17.5" y="280" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-72" parent="E3uyBDmK6ujrw01T13Bo-71" style="fillColor=none;strokeColor=#147EBA;dashed=1;verticalAlign=top;fontStyle=0;fontColor=#147EBA;whiteSpace=wrap;html=1;" value="Availability Zone" vertex="1">
          <mxGeometry height="290" width="371.25" x="28.75" y="230" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-73" parent="E3uyBDmK6ujrw01T13Bo-71" style="points=[[0,0],[0.25,0],[0.5,0],[0.75,0],[1,0],[1,0.25],[1,0.5],[1,0.75],[1,1],[0.75,1],[0.5,1],[0.25,1],[0,1],[0,0.75],[0,0.5],[0,0.25]];outlineConnect=0;gradientColor=none;html=1;whiteSpace=wrap;fontSize=12;fontStyle=0;container=1;pointerEvents=0;collapsible=0;recursiveResize=0;shape=mxgraph.aws4.group;grIcon=mxgraph.aws4.group_vpc2;strokeColor=#8C4FFF;fillColor=none;verticalAlign=top;align=left;spacingLeft=30;fontColor=#AAB7B8;dashed=0;" value="VPC" vertex="1">
          <mxGeometry height="510" width="514.5" x="20" y="30" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-74" parent="E3uyBDmK6ujrw01T13Bo-73" style="fillColor=none;strokeColor=#147EBA;dashed=1;verticalAlign=top;fontStyle=0;fontColor=#147EBA;whiteSpace=wrap;html=1;" value="Availability Zone" vertex="1">
          <mxGeometry height="140" width="371.25" x="8.75" y="40" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-75" parent="E3uyBDmK6ujrw01T13Bo-73" style="points=[[0,0],[0.25,0],[0.5,0],[0.75,0],[1,0],[1,0.25],[1,0.5],[1,0.75],[1,1],[0.75,1],[0.5,1],[0.25,1],[0,1],[0,0.75],[0,0.5],[0,0.25]];outlineConnect=0;gradientColor=none;html=1;whiteSpace=wrap;fontSize=12;fontStyle=0;container=1;pointerEvents=0;collapsible=0;recursiveResize=0;shape=mxgraph.aws4.group;grIcon=mxgraph.aws4.group_security_group;grStroke=0;strokeColor=#00A4A6;fillColor=#E6F6F7;verticalAlign=top;align=left;spacingLeft=30;fontColor=#147EBA;dashed=0;" value="Private subnet" vertex="1">
          <mxGeometry height="277" width="117.5" x="252.5" y="53" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-76" parent="E3uyBDmK6ujrw01T13Bo-73" style="fillColor=none;strokeColor=#DD3522;verticalAlign=top;fontStyle=0;fontColor=#DD3522;whiteSpace=wrap;html=1;" value="Security group" vertex="1">
          <mxGeometry height="240" width="107.5" x="262.5" y="80" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-202" edge="1" parent="E3uyBDmK6ujrw01T13Bo-73" source="E3uyBDmK6ujrw01T13Bo-77" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;endArrow=none;endFill=0;startArrow=classic;startFill=1;" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="442.5" y="280" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-77" parent="E3uyBDmK6ujrw01T13Bo-73" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#C925D1;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.aurora;" value="Aurora" vertex="1">
          <mxGeometry height="78" width="78" x="278.5" y="100" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-78" parent="E3uyBDmK6ujrw01T13Bo-73" style="points=[[0,0],[0.25,0],[0.5,0],[0.75,0],[1,0],[1,0.25],[1,0.5],[1,0.75],[1,1],[0.75,1],[0.5,1],[0.25,1],[0,1],[0,0.75],[0,0.5],[0,0.25]];outlineConnect=0;gradientColor=none;html=1;whiteSpace=wrap;fontSize=12;fontStyle=0;container=1;pointerEvents=0;collapsible=0;recursiveResize=0;shape=mxgraph.aws4.group;grIcon=mxgraph.aws4.group_security_group;grStroke=0;strokeColor=#7AA116;fillColor=#F2F6E8;verticalAlign=top;align=left;spacingLeft=30;fontColor=#248814;dashed=0;" value="Public subnet" vertex="1">
          <mxGeometry height="430" width="117.5" x="22.5" y="50" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-83" parent="E3uyBDmK6ujrw01T13Bo-78" style="sketch=0;outlineConnect=0;fontColor=#232F3E;gradientColor=none;fillColor=#ED7100;strokeColor=none;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;pointerEvents=1;shape=mxgraph.aws4.auto_scaling2;" value="Auto Scaling" vertex="1">
          <mxGeometry height="48" width="48" x="34.75" y="144" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-82" parent="E3uyBDmK6ujrw01T13Bo-78" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#ED7100;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.ec2;" value="EC2" vertex="1">
          <mxGeometry height="78" width="78" x="19.75" y="220" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-79" parent="E3uyBDmK6ujrw01T13Bo-78" style="fillColor=none;strokeColor=#DD3522;verticalAlign=top;fontStyle=0;fontColor=#DD3522;whiteSpace=wrap;html=1;" value="Security group" vertex="1">
          <mxGeometry height="412" width="97.5" x="10" y="18" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-80" parent="E3uyBDmK6ujrw01T13Bo-73" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#ED7100;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.ec2;" value="EC2" vertex="1">
          <mxGeometry height="78" width="78" x="42.25" y="92" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-84" parent="E3uyBDmK6ujrw01T13Bo-73" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#C925D1;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.dynamodb;" value="DynamoDB" vertex="1">
          <mxGeometry height="78" width="78" x="278.5" y="218" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-136" edge="1" parent="E3uyBDmK6ujrw01T13Bo-73" source="E3uyBDmK6ujrw01T13Bo-80" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0.047;entryY=0.21;entryDx=0;entryDy=0;exitX=1;exitY=0.5;exitDx=0;exitDy=0;exitPerimeter=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-76" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="200.25" y="309" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-192" edge="1" parent="E3uyBDmK6ujrw01T13Bo-71" source="E3uyBDmK6ujrw01T13Bo-85" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0.5;entryY=1;entryDx=0;entryDy=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-84" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="220.25" y="448.9999999999998" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-85" parent="E3uyBDmK6ujrw01T13Bo-71" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#ED7100;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.lambda;" value="Lambda" vertex="1">
          <mxGeometry height="78" width="78" x="62.25" y="410" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-86" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-88" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0.5;entryY=0;entryDx=0;entryDy=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-80" value="">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-87" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-88" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0.5;entryY=0;entryDx=0;entryDy=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-77" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="232" y="350" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-88" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#8C4FFF;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.elastic_load_balancing;" value="ELB" vertex="1">
          <mxGeometry height="78" width="78" x="180" y="180" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-200" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-92" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;endArrow=none;endFill=0;" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="610" y="690" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-255" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-92" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;endArrow=none;endFill=0;" value="">
          <mxGeometry relative="1" as="geometry">
            <Array as="points">
              <mxPoint x="800" y="809" />
              <mxPoint x="800" y="690" />
            </Array>
            <mxPoint x="808" y="690" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-92" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#E7157B;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.cloudwatch_2;" value="CloudWatch" vertex="1">
          <mxGeometry height="78" width="78" x="698" y="770" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-93" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#E7157B;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.trusted_advisor;" value="Trusted Advisor" vertex="1">
          <mxGeometry height="78" width="78" x="698" y="650" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-94" parent="E3uyBDmK6ujrw01T13Bo-61" style="shape=image;editableCssRules=.*;verticalLabelPosition=bottom;labelBackgroundColor=default;verticalAlign=top;aspect=fixed;imageAspect=0;image=data:image/svg+xml,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9Ii0wLjUgLTAuNSAyOC4wMDIgMjgiIGhlaWdodD0iMjgiIHdpZHRoPSIyOC4wMDIiPiYjeGE7CSAgPHN0eWxlIHR5cGU9InRleHQvY3NzIj4mI3hhOwkJLnN0MHtzdG9wLWNvbG9yOiMwMGNlYWM7fSYjeGE7CQkuc3Qxe3N0b3AtY29sb3I6IzA3ODM4Zjt9JiN4YTsJICA8L3N0eWxlPiYjeGE7CTxkZWZzPiYjeGE7CQk8bGluZWFyR3JhZGllbnQgZ3JhZGllbnRVbml0cz0idXNlclNwYWNlT25Vc2UiIHkyPSIyNS4zMSIgeDI9IjIzLjQ1NCIgeTE9IjMuNDc0IiB4MT0iMi4wMzkiIGlkPSJBIj4mI3hhOwkJCTxzdG9wIGNsYXNzPSJzdDAiLz4mI3hhOwkJCTxzdG9wIGNsYXNzPSJzdDEiIG9mZnNldD0iMSIvPiYjeGE7CQk8L2xpbmVhckdyYWRpZW50PiYjeGE7CTwvZGVmcz4mI3hhOwk8cGF0aCBmaWxsPSJ1cmwoI0EpIiBkPSJNMjcuMDYzIDIwLjkyNGMuMjkzLjI5My40MzguNjIxLjQzOC45ODd2NC4xNjVjMCAuOTQ5LS40NzQgMS40MjQtMS40MjYgMS40MjRoLTQuMTYzYy0uMTg2LS4wMDItLjM2OS0uMDQyLS41MzktLjExN3MtLjMyMi0uMTg1LS40NDgtLjMyMWwtLjk4Ny0uOTg1aC0yLjI0NGExLjUzIDEuNTMgMCAwIDEtMS4wNDMtLjM4NGMtLjEyOC0uMTMtLjIyNy0uMjg0LS4yOTMtLjQ1NHMtLjA5Ni0uMzUxLS4wODktLjUzM3YtMS40MjRoLTEuMzdhMS41MyAxLjUzIDAgMCAxLTEuMDQyLS4zODRjLS4xMjgtLjEzLS4yMjgtLjI4NC0uMjk0LS40NTNzLS4wOTctLjM1MS0uMDktLjUzMnYtMS40MjZoLTEuMzdjLS45NSAwLTEuNDI2LS40NzMtMS40MjYtMS40MjRWMTYuODdsLS41NDctLjYwM0g3Ljg4NGMtMS4xMzEgMC0yLjIwOS0uMjE5LTMuMjMyLS42NThhOC40OCA4LjQ4IDAgMCAxLTIuNjg1LTEuODA4Yy0uNzcxLS43Ny0xLjM4Ni0xLjY4Mi0xLjgxMS0yLjY4NUE4LjEyIDguMTIgMCAwIDEtLjUgNy44ODRjMC0xLjE3LjIxOS0yLjI0Ni42NTgtMy4yMzMuNDI1LTEuMDAzIDEuMDM5LTEuOTE0IDEuODA4LTIuNjg1UzMuNjUuNTgyIDQuNjU0LjE1OEE4LjEyIDguMTIgMCAwIDEgNy44ODUtLjVjMS4xNjggMCAyLjI0NC4yMTkgMy4yMzIuNjU4IDEuMDI0LjQzOCAxLjkxOCAxLjA0IDIuNjg2IDEuODA2czEuMzY4IDEuNjY0IDEuODA2IDIuNjg2Yy40MzguOTg3LjY1OSAyLjA2NC42NTkgMy4yMzN2Mi4yNDV6bS0yLjM1NyAxLjUzNEwxMy45MTIgMTEuNjY0YTEuMzYgMS4zNiAwIDAgMS0uMzIxLS40NDdjLS4wNzUtLjE2OS0uMTE1LS4zNTItLjExOC0uNTM4VjcuODg0YzAtLjc2OC0uMTQ2LTEuNDgtLjQzOC0yLjEzOC0uMjgtLjY3Ni0uNjktMS4yOS0xLjIwNy0xLjgwOGE1LjYgNS42IDAgMCAwLTEuODA2LTEuMjA2IDUuMiA1LjIgMCAwIDAtMi4xMzktLjQzOGMtLjc2NiAwLTEuNDk3LjE0Ny0yLjE5Mi40MzhhNS43OCA1Ljc4IDAgMCAwLTEuNzUzIDEuMjA2IDUuNjEgNS42MSAwIDAgMC0xLjIwNSAxLjgwOGMtLjI5My42NTgtLjQzOCAxLjM2OS0uNDM4IDIuMTM4cy4xNDYgMS40OTcuNDM4IDIuMTkyYTUuNzkgNS43OSAwIDAgMCAxLjIwNiAxLjc1NGMuNTEuNTEgMS4wOTQuOTEyIDEuNzU0IDEuMjA1LjY5My4yOTMgMS40MjQuNDM4IDIuMTkyLjQzOGgyLjc5M2MuMzY3IDAgLjY5NC4xNDcuOTg3LjQzOGwxLjQyNCAxLjQyNmMuMTI0LjEyLjIyMi4yNjUuMjg4LjQyNWExLjI3IDEuMjcgMCAwIDEgLjA5Ni41MDR2MS40MjZoMS40MjZjLjkxMiAwIDEuMzY4LjQ1NiAxLjM2OCAxLjM2OHYxLjQyNWgxLjQyNmMuNDAzIDAgLjczMS4xMjguOTg3LjM4NC4yOTEuMjU2LjQzOC42MDMuNDM4IDEuMDQxdjEuMzY5aDEuMzY5Yy4zNjUgMCAuNjk0LjE0Ni45ODcuNDM4bC45ODUuOTg3aDIuMjQ2ek03Ljg4NCA1LjA4OGMuNzY4IDAgMS40MjYuMjc0IDEuOTczLjgyMnMuODI0IDEuMjA1LjgyNCAxLjk3M2EyLjY5IDIuNjkgMCAwIDEtLjgyNCAxLjk3MyAyLjY5IDIuNjkgMCAwIDEtMS45NzMuODIyIDIuNjkgMi42OSAwIDAgMS0xLjk3My0uODI0IDIuNjkgMi42OSAwIDAgMS0uODE5LTEuOTcxYzAtLjc2OC4yNzQtMS40MjYuODIyLTEuOTczYTIuNjkgMi42OSAwIDAgMSAxLjk3MS0uODIyeiIvPiYjeGE7PC9zdmc+;rotation=45;" value="" vertex="1">
          <mxGeometry height="60" width="60" x="870" y="18" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-95" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#DD344C;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.key_management_service;" value="Key&amp;nbsp;&lt;br&gt;Management&lt;div&gt;Service&lt;/div&gt;" vertex="1">
          <mxGeometry height="78" width="78" x="950" y="18" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-196" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-96" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=1;entryY=0.75;entryDx=0;entryDy=0;entryPerimeter=0;endArrow=none;endFill=0;startArrow=classic;startFill=1;" target="E3uyBDmK6ujrw01T13Bo-64" value="">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-96" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#DD344C;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.waf;" value="WAF" vertex="1">
          <mxGeometry height="78" width="78" x="752" y="110" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-98" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;" target="E3uyBDmK6ujrw01T13Bo-70" value="">
          <mxGeometry relative="1" as="geometry">
            <Array as="points">
              <mxPoint x="620" y="569" />
            </Array>
            <mxPoint x="392.75" y="569" as="sourcePoint" />
            <mxPoint x="353.75" y="688" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-137" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-82" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;" target="E3uyBDmK6ujrw01T13Bo-70" value="">
          <mxGeometry relative="1" as="geometry">
            <Array as="points">
              <mxPoint x="650" y="640" />
            </Array>
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-138" parent="E3uyBDmK6ujrw01T13Bo-61" style="rounded=0;whiteSpace=wrap;html=1;strokeColor=#FFFFFF;" value="&lt;span style=&quot;text-wrap-mode: nowrap; background-color: rgb(255, 255, 255);&quot;&gt;AWS IAM&lt;/span&gt;" vertex="1">
          <mxGeometry height="22" width="60" x="870" y="98" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-198" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-93" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;exitX=0;exitY=0.5;exitDx=0;exitDy=0;exitPerimeter=0;" value="">
          <mxGeometry relative="1" as="geometry">
            <Array as="points">
              <mxPoint x="610" y="689" />
              <mxPoint x="610" y="587" />
            </Array>
            <mxPoint x="737" y="650" as="sourcePoint" />
            <mxPoint x="394" y="587.0000000000001" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-218" parent="E3uyBDmK6ujrw01T13Bo-61" style="points=[[0,0],[0.25,0],[0.5,0],[0.75,0],[1,0],[1,0.25],[1,0.5],[1,0.75],[1,1],[0.75,1],[0.5,1],[0.25,1],[0,1],[0,0.75],[0,0.5],[0,0.25]];outlineConnect=0;gradientColor=none;html=1;whiteSpace=wrap;fontSize=12;fontStyle=0;container=1;pointerEvents=0;collapsible=0;recursiveResize=0;shape=mxgraph.aws4.group;grIcon=mxgraph.aws4.group_security_group;grStroke=0;strokeColor=#00A4A6;fillColor=#E6F6F7;verticalAlign=top;align=left;spacingLeft=30;fontColor=#147EBA;dashed=0;" value="Private subnet" vertex="1">
          <mxGeometry height="277" width="117.5" x="860" y="360" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-219" parent="E3uyBDmK6ujrw01T13Bo-61" style="fillColor=none;strokeColor=#DD3522;verticalAlign=top;fontStyle=0;fontColor=#DD3522;whiteSpace=wrap;html=1;" value="Security group" vertex="1">
          <mxGeometry height="240" width="107.5" x="870" y="387" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-259" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-221" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;endArrow=none;endFill=0;startArrow=classic;startFill=1;" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="830" y="570" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-221" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#C925D1;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.aurora;" value="Aurora" vertex="1">
          <mxGeometry height="78" width="78" x="886" y="407" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-222" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#C925D1;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.dynamodb;" value="DynamoDB" vertex="1">
          <mxGeometry height="78" width="78" x="886" y="525" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-223" parent="E3uyBDmK6ujrw01T13Bo-61" style="points=[[0,0],[0.25,0],[0.5,0],[0.75,0],[1,0],[1,0.25],[1,0.5],[1,0.75],[1,1],[0.75,1],[0.5,1],[0.25,1],[0,1],[0,0.75],[0,0.5],[0,0.25]];outlineConnect=0;gradientColor=none;html=1;whiteSpace=wrap;fontSize=12;fontStyle=0;container=1;pointerEvents=0;collapsible=0;recursiveResize=0;shape=mxgraph.aws4.group;grIcon=mxgraph.aws4.group_security_group;grStroke=0;strokeColor=#7AA116;fillColor=#F2F6E8;verticalAlign=top;align=left;spacingLeft=30;fontColor=#248814;dashed=0;" value="Public subnet" vertex="1">
          <mxGeometry height="430" width="117.5" x="1090" y="360" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-224" parent="E3uyBDmK6ujrw01T13Bo-223" style="sketch=0;outlineConnect=0;fontColor=#232F3E;gradientColor=none;fillColor=#ED7100;strokeColor=none;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;pointerEvents=1;shape=mxgraph.aws4.auto_scaling2;" value="Auto Scaling" vertex="1">
          <mxGeometry height="48" width="48" x="34.75" y="144" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-225" parent="E3uyBDmK6ujrw01T13Bo-223" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#ED7100;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.ec2;" value="EC2" vertex="1">
          <mxGeometry height="78" width="78" x="19.75" y="220" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-226" parent="E3uyBDmK6ujrw01T13Bo-223" style="fillColor=none;strokeColor=#DD3522;verticalAlign=top;fontStyle=0;fontColor=#DD3522;whiteSpace=wrap;html=1;" value="Security group" vertex="1">
          <mxGeometry height="412" width="97.5" x="10" y="18" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-248" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-227" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0.968;entryY=0.417;entryDx=0;entryDy=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-221" value="">
          <mxGeometry relative="1" as="geometry">
            <Array as="points">
              <mxPoint x="1037" y="440" />
            </Array>
            <mxPoint x="1029.75" y="441" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-227" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#ED7100;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.ec2;" value="EC2" vertex="1">
          <mxGeometry height="78" width="78" x="1109.75" y="402" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-261" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-228" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0.5;entryY=1;entryDx=0;entryDy=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-222" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="1029.75" y="728.9999999999998" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-228" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#ED7100;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.lambda;" value="Lambda" vertex="1">
          <mxGeometry height="78" width="78" x="1109.75" y="690" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-244" parent="E3uyBDmK6ujrw01T13Bo-61" style="points=[[0,0],[0.25,0],[0.5,0],[0.75,0],[1,0],[1,0.25],[1,0.5],[1,0.75],[1,1],[0.75,1],[0.5,1],[0.25,1],[0,1],[0,0.75],[0,0.5],[0,0.25]];outlineConnect=0;gradientColor=none;html=1;whiteSpace=wrap;fontSize=12;fontStyle=0;container=1;pointerEvents=0;collapsible=0;recursiveResize=0;shape=mxgraph.aws4.group;grIcon=mxgraph.aws4.group_vpc2;strokeColor=#8C4FFF;fillColor=none;verticalAlign=top;align=left;spacingLeft=30;fontColor=#AAB7B8;dashed=0;" value="VPC" vertex="1">
          <mxGeometry height="510" width="460" x="840" y="310" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-246" parent="E3uyBDmK6ujrw01T13Bo-61" style="points=[[0,0],[0.25,0],[0.5,0],[0.75,0],[1,0],[1,0.25],[1,0.5],[1,0.75],[1,1],[0.75,1],[0.5,1],[0.25,1],[0,1],[0,0.75],[0,0.5],[0,0.25]];outlineConnect=0;gradientColor=none;html=1;whiteSpace=wrap;fontSize=12;fontStyle=0;container=1;pointerEvents=0;collapsible=0;recursiveResize=0;shape=mxgraph.aws4.group;grIcon=mxgraph.aws4.group_region;strokeColor=#00A4A6;fillColor=none;verticalAlign=top;align=left;spacingLeft=30;fontColor=#147EBA;dashed=1;" value="Region" vertex="1">
          <mxGeometry height="560" width="520" x="810" y="280" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-251" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-249" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0.5;entryY=0;entryDx=0;entryDy=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-221" value="">
          <mxGeometry relative="1" as="geometry">
            <mxPoint x="980" y="219" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-253" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-249" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0.5;entryY=0;entryDx=0;entryDy=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-227" value="">
          <mxGeometry relative="1" as="geometry">
            <Array as="points">
              <mxPoint x="1150" y="219" />
              <mxPoint x="1150" y="402" />
            </Array>
            <mxPoint x="1218" y="219" as="targetPoint" />
          </mxGeometry>
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-249" parent="E3uyBDmK6ujrw01T13Bo-61" style="sketch=0;points=[[0,0,0],[0.25,0,0],[0.5,0,0],[0.75,0,0],[1,0,0],[0,1,0],[0.25,1,0],[0.5,1,0],[0.75,1,0],[1,1,0],[0,0.25,0],[0,0.5,0],[0,0.75,0],[1,0.25,0],[1,0.5,0],[1,0.75,0]];outlineConnect=0;fontColor=#232F3E;fillColor=#8C4FFF;strokeColor=#ffffff;dashed=0;verticalLabelPosition=bottom;verticalAlign=top;align=center;html=1;fontSize=12;fontStyle=0;aspect=fixed;shape=mxgraph.aws4.resourceIcon;resIcon=mxgraph.aws4.elastic_load_balancing;" value="ELB" vertex="1">
          <mxGeometry height="78" width="78" x="1020" y="180" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-254" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-93" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0;entryY=0.5;entryDx=0;entryDy=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-222" value="">
          <mxGeometry relative="1" as="geometry" />
        </mxCell>
        <mxCell id="E3uyBDmK6ujrw01T13Bo-265" edge="1" parent="E3uyBDmK6ujrw01T13Bo-61" source="E3uyBDmK6ujrw01T13Bo-226" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;html=1;entryX=0.75;entryY=1;entryDx=0;entryDy=0;entryPerimeter=0;" target="E3uyBDmK6ujrw01T13Bo-70" value="">
          <mxGeometry relative="1" as="geometry">
            <Array as="points">
              <mxPoint x="679" y="640" />
            </Array>
            <mxPoint x="1020" y="584" as="targetPoint" />
          </mxGeometry>
        </mxCell>
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>

<img width="1391" height="1080" alt="3D E-Commerce Platform-Updated drawio (1)" src="https://github.com/user-attachments/assets/378d0282-2929-45a3-b9f6-39319a8f4cad" />


---

## How the Architecture Works - Flow from User to Data

The diagram is read **top to bottom**, following the path of a real user request.

### 1. Users → Internet → Route 53

A user opens the browser and types the site address. **Route 53** (AWS DNS service) translates the domain name into a server address and routes the user to the nearest AWS region. This is always the first element in any AWS architecture - without DNS, the browser has no idea where to go.

### 2. Route 53 → CloudFront → S3

**CloudFront** is a Content Delivery Network with 400+ edge locations worldwide. 3D model files are large - tens of megabytes. Instead of downloading them from one central server, CloudFront caches them at the edge location closest to the user (e.g. Frankfurt for Warsaw). **S3** stores the original 3D models, product images, and static files. CloudFront fetches from S3 and serves cached copies globally. This directly addresses the **Performance** requirement.

### 3. CloudFront → AWS Shield + WAF

All traffic passing through CloudFront is protected by two security layers:

- **AWS Shield** - protects against DDoS attacks (floods of fake traffic designed to crash the system)
- **WAF (Web Application Firewall)** - filters malicious requests before they reach the servers

These sit at the edge - threats are blocked before they ever enter the internal network.

### 4. Route 53 → ELB (one per VPC)

Dynamic user actions - login, cart, checkout - go to the **Elastic Load Balancer**. The ELB distributes incoming requests evenly across EC2 instances so no single server gets overloaded. Two ELBs are deployed, one per VPC, supporting the multi-region design described below.

### 5. ELB → VPC → EC2 + Auto Scaling

The architecture uses **two separate VPCs**, each deployed in its own AWS Region.

**Why two VPCs and two ELBs?**
This design provides **geographic redundancy** - if an entire AWS region goes down, the second VPC in a different region continues serving users without interruption. This is a stronger form of resilience than a single-region Multi-AZ setup and directly satisfies the **High Availability** requirement at regional scale. Each VPC has its own ELB because each region operates as an independent, fully functional unit.

Inside each VPC:

- **Public subnet** - EC2 application servers with Auto Scaling. When traffic spikes, Auto Scaling launches additional instances automatically. When it drops, they shut down. This satisfies **Scalability** and **Cost Optimization**.
- **Two Availability Zones** inside each VPC - an extra layer of redundancy within the region itself.

### 6. EC2 → Aurora + DynamoDB (Private Subnets)

Application servers write and read from two databases, both in **private subnets** - not reachable from the internet:

- **Amazon Aurora (RDS)** - relational database for transactional data: customer accounts, orders, payment records. Chosen because orders require strict consistency - money cannot be charged without a confirmed order record.
- **Amazon DynamoDB** - NoSQL database for the product catalog and user sessions. Delivers single-digit millisecond reads at any scale - essential when millions of users browse products simultaneously.

Both databases are replicated across Availability Zones within each VPC.

### 7. EC2 → Lambda

**AWS Lambda** handles lightweight event-driven tasks: sending order confirmation emails, updating stock counts, notifying the warehouse. Lambda runs only when triggered and costs nothing when idle - a direct contribution to **Cost Optimization**.

### 8. Security - AWS IAM + KMS

Applied across the entire architecture:

- **AWS IAM** - every service has a role with minimum required permissions. EC2 cannot access S3 arbitrarily. Lambda cannot read the database directly. Least-privilege access is enforced throughout.
- **AWS Key Management Service (KMS)** - manages encryption keys for data at rest across S3, RDS, and DynamoDB.

### 9. Monitoring - CloudWatch + Trusted Advisor

- **Amazon CloudWatch** - monitors CPU usage, error rates, database response times, and Lambda execution across both VPCs. Triggers alarms when metrics exceed thresholds.
- **AWS Trusted Advisor** - runs daily checks for cost inefficiencies, idle resources, and security gaps. Ensures the platform stays lean and secure over time.

---

## How the Architecture Meets Each Requirement

| Requirement | How it is addressed |
|---|---|
| **High Availability** | Two separate VPCs in two AWS Regions. If one region fails, the second continues. Two Availability Zones per VPC add a second redundancy layer. |
| **Scalability** | EC2 Auto Scaling in both VPCs. DynamoDB scales automatically. No manual action needed during traffic spikes. |
| **Performance** | CloudFront serves 3D assets from the edge location nearest to each user. DynamoDB delivers millisecond reads for the product catalog. |
| **Security** | WAF and Shield at the edge. Private subnets for all databases. IAM least-privilege roles. KMS encryption at rest. HTTPS via CloudFront. |
| **Cost Optimization** | Auto Scaling prevents over-provisioning. Lambda eliminates idle servers for background tasks. Trusted Advisor identifies unused resources. |

---

## Design Decisions and Trade-offs

**Two VPCs instead of one Multi-AZ VPC**
The architecture uses two fully separate VPCs across two AWS Regions rather than a single VPC with two Availability Zones. This provides regional-level fault tolerance - protection against a full region outage, not just a single data center failure. The trade-off is higher operational complexity and cost. For a global platform serving millions of users, this is a justified design choice.

**Two databases instead of one**
Aurora handles transactional data where accuracy is critical. DynamoDB handles high-volume reads where speed matters more than relational structure. Using both means each database does exactly what it is built for.

**Lambda alongside EC2**
EC2 runs the core application logic continuously. Lambda handles infrequent event-triggered tasks. Running those tasks on EC2 would mean paying for a server that sits idle most of the time.

---

## What I Learned

Before this project, AWS felt like a long and disconnected list of service names. Designing this architecture from scratch changed that completely.

The most important thing I learned is that **architecture is not a list of services - it is the story of a user's journey**. Once I traced what actually happens when someone opens a website and clicks on a 3D model, every service stopped being an abstract name and became a logical answer to a specific problem. CloudFront exists because 3D files are heavy and users are everywhere. Two Availability Zones exist because a single data center can lose power. Lambda exists because not every task needs a server running around the clock.

I also learned the difference between **availability and scalability** - two words I used interchangeably before. Availability is about staying online when something breaks. Scalability is about handling more load when more people arrive. They are solved by different services and different design decisions.

Designing the security layer taught me that **security is not one decision - it is many layers**. WAF blocks bad traffic at the edge. Private subnets hide the servers from the internet. IAM limits what each service can do internally. KMS encrypts the data itself. Each layer catches what the previous one might miss.

Finally, I understood that **cost optimization is a design decision, not something you fix after launch**. Choosing Lambda over EC2 for background tasks, using Auto Scaling instead of fixed capacity, storing assets in S3 - these choices directly affect the monthly bill and are made at the architecture stage, before a single server is turned on.

This was my first time thinking like an architect rather than a user. I now look at every website differently.

---

## Tools Used

- [draw.io](https://app.diagrams.net/) - architecture diagram with built-in AWS icon library

## Program

AWS Cloud Practitioner preparation · Bring Women Back to Work · Salesforce Switzerland
