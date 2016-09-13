# activiti

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL" xmlns:activiti="http://activiti.org/bpmn" xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI" xmlns:omgdc="http://www.omg.org/spec/DD/20100524/DC" xmlns:omgdi="http://www.omg.org/spec/DD/20100524/DI" xmlns:tns="http://www.activiti.org/test" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" expressionLanguage="http://www.w3.org/1999/XPath" id="m1472438252635" name="" targetNamespace="http://www.activiti.org/test" typeLanguage="http://www.w3.org/2001/XMLSchema">
  <process id="themeApprove" isClosed="false" isExecutable="true" name="数据主题订阅新流程" processType="None">
    <startEvent id="start" name="开始"/>
    <userTask activiti:exclusive="true" id="DeptApprove" name="数据提供方部门领导审批">
      <extensionElements>
        <activiti:taskListener class="com.zte.ums.beas.subscription.activiti.extend.DataThemeFirstTaskListener" event="create"/>
      </extensionElements>
    </userTask>
    <exclusiveGateway gatewayDirection="Unspecified" id="wg2"/>
    <sequenceFlow id="_6" sourceRef="DeptApprove" targetRef="wg2"/>
    <userTask activiti:exclusive="true" id="SecondApprove" name="中心部门领导审批或数据提供方部门领导撤回">
      <extensionElements>
        <activiti:taskListener class="com.zte.ums.beas.subscription.activiti.extend.DataThemeSecondTaskListener" event="create"/>
      </extensionElements>
    </userTask>
    <userTask activiti:assignee="${creator}" activiti:exclusive="true" id="CreatorModify" name="订阅者修改提交"/>
    <exclusiveGateway gatewayDirection="Unspecified" id="wg1"/>
    <endEvent id="end" name="结束"/>
    <exclusiveGateway gatewayDirection="Unspecified" id="_22"/>
    <userTask activiti:exclusive="true" id="AllCancel" name="撤销">
      <extensionElements>
        <activiti:taskListener class="com.zte.ums.beas.subscription.activiti.extend.DataThemeThirdTaskListener" event="create"/>
      </extensionElements>
    </userTask>
    <sequenceFlow id="_25" sourceRef="SecondApprove" targetRef="_22"/>
    <intermediateCatchEvent id="timer" name="延迟撤销定时器">
      <timerEventDefinition id="timeDate">
        <timeDate><![CDATA[${delayDate}]]></timeDate>
      </timerEventDefinition>
    </intermediateCatchEvent>
    <serviceTask activiti:class="com.zte.ums.beas.subscription.activiti.extend.ChangeStatusServiceTask" activiti:exclusive="true" id="ChangeStatus" name="修改订阅状态"/>
    <sequenceFlow id="_38" sourceRef="timer" targetRef="ChangeStatus"/>
    <sequenceFlow id="_39" sourceRef="ChangeStatus" targetRef="CreatorModify"/>
    <serviceTask activiti:class="com.zte.ums.beas.subscription.activiti.extend.NoticeServiceTask" activiti:exclusive="true" id="notice6" name="邮件短信通知"/>
    <sequenceFlow id="_41" sourceRef="AllCancel" targetRef="notice6"/>
    <sequenceFlow id="_42" sourceRef="notice6" targetRef="timer"/>
    <serviceTask activiti:class="com.zte.ums.beas.subscription.activiti.extend.NoticeServiceTask" activiti:exclusive="true" id="notice4" name="邮件短信通知"/>
    <sequenceFlow id="_44" sourceRef="notice4" targetRef="AllCancel"/>
    <sequenceFlow id="_45" name="同意" sourceRef="_22" targetRef="notice4">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${secondAgree==1}]]></conditionExpression>
    </sequenceFlow>
    <serviceTask activiti:class="com.zte.ums.beas.subscription.activiti.extend.NoticeServiceTask" activiti:exclusive="true" id="notice2" name="邮件短信通知"/>
    <sequenceFlow id="_47" sourceRef="notice2" targetRef="SecondApprove"/>
    <sequenceFlow id="_48" name="同意" sourceRef="wg2" targetRef="notice2">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${deptAgree==1}]]></conditionExpression>
    </sequenceFlow>
    <serviceTask activiti:class="com.zte.ums.beas.subscription.activiti.extend.NoticeServiceTask" activiti:exclusive="true" id="notice1" name="邮件短信通知"/>
    <sequenceFlow id="_50" name="修改" sourceRef="wg1" targetRef="notice1">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[ ${creatorModify==1}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="_51" sourceRef="notice1" targetRef="DeptApprove"/>
    <serviceTask activiti:class="com.zte.ums.beas.subscription.activiti.extend.NoticeServiceTask" activiti:exclusive="true" id="notice5" name="邮件短信通知"/>
    <sequenceFlow id="_56" name="不同意" sourceRef="_22" targetRef="notice5">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${secondAgree==0}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="_57" sourceRef="notice5" targetRef="CreatorModify"/>
    <serviceTask activiti:class="com.zte.ums.beas.subscription.activiti.extend.NoticeServiceTask" activiti:exclusive="true" id="delete" name="删除订阅"/>
    <sequenceFlow id="_60" name="删除" sourceRef="wg1" targetRef="delete">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${creatorModify==0}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="_4" sourceRef="delete" targetRef="end"/>
    <boundaryEvent attachedToRef="AllCancel" cancelActivity="true" id="BoundaryEvent2" name="定时边界事件">
      <timerEventDefinition id="BoundaryEvent2_ED_1">
        <timeDate><![CDATA[${secondEndDate}]]></timeDate>
      </timerEventDefinition>
    </boundaryEvent>
    <boundaryEvent attachedToRef="SecondApprove" cancelActivity="true" id="BoundaryEvent1" name="定时边界事件">
      <timerEventDefinition id="BoundaryEvent1_ED_1">
        <timeDate><![CDATA[${firstEndDate}]]></timeDate>
      </timerEventDefinition>
    </boundaryEvent>
    <sequenceFlow id="_8" sourceRef="BoundaryEvent2" targetRef="ChangeStatus"/>
    <sequenceFlow id="_9" sourceRef="BoundaryEvent1" targetRef="ChangeStatus"/>
    <serviceTask activiti:class="com.zte.ums.beas.subscription.activiti.extend.NoticeServiceTask" activiti:exclusive="true" id="notice7" name="邮件短信通知"/>
    <sequenceFlow id="_3" sourceRef="notice7" targetRef="CreatorModify"/>
    <sequenceFlow id="_5" name="不同意" sourceRef="wg2" targetRef="notice7">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${deptAgree==0}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="_2" name="撤回" sourceRef="_22" targetRef="DeptApprove">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${secondAgree==2}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="_7" sourceRef="start" targetRef="CreatorModify"/>
    <sequenceFlow id="_10" sourceRef="CreatorModify" targetRef="wg1"/>
  </process>
  <bpmndi:BPMNDiagram documentation="background=#3C3F41;count=1;horizontalcount=1;orientation=0;width=842.4;height=1195.2;imageableWidth=832.4;imageableHeight=1185.2;imageableX=5.0;imageableY=5.0" id="Diagram-_1" name="New Diagram">
    <bpmndi:BPMNPlane bpmnElement="themeApprove">
      <bpmndi:BPMNShape bpmnElement="start" id="Shape-start">
        <omgdc:Bounds height="32.0" width="32.0" x="0.0" y="155.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="32.0" width="32.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="DeptApprove" id="Shape-DeptApprove">
        <omgdc:Bounds height="105.0" width="145.0" x="445.0" y="125.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="105.0" width="145.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="wg2" id="Shape-wg2" isMarkerVisible="false">
        <omgdc:Bounds height="32.0" width="32.0" x="605.0" y="165.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="32.0" width="32.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="SecondApprove" id="Shape-SecondApprove">
        <omgdc:Bounds height="110.0" width="150.0" x="790.0" y="120.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="110.0" width="150.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="CreatorModify" id="Shape-CreatorModify">
        <omgdc:Bounds height="75.0" width="120.0" x="65.0" y="135.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="75.0" width="120.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="wg1" id="Shape-wg1" isMarkerVisible="false">
        <omgdc:Bounds height="32.0" width="32.0" x="225.0" y="160.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="32.0" width="32.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="end" id="Shape-end">
        <omgdc:Bounds height="32.0" width="32.0" x="360.0" y="40.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="32.0" width="32.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="_22" id="Shape-_22" isMarkerVisible="false">
        <omgdc:Bounds height="32.0" width="32.0" x="960.0" y="165.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="32.0" width="32.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="AllCancel" id="Shape-AllCancel">
        <omgdc:Bounds height="87.0" width="95.0" x="1215.0" y="140.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="87.0" width="95.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="timer" id="Shape-timer">
        <omgdc:Bounds height="32.0" width="32.0" x="1115.0" y="390.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="32.0" width="32.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="ChangeStatus" id="Shape-ChangeStatus">
        <omgdc:Bounds height="55.0" width="85.0" x="810.0" y="375.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="55.0" width="85.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="notice6" id="Shape-notice6">
        <omgdc:Bounds height="55.0" width="85.0" x="1220.0" y="370.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="55.0" width="85.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="notice4" id="Shape-notice4">
        <omgdc:Bounds height="55.0" width="85.0" x="1055.0" y="155.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="55.0" width="85.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="notice2" id="Shape-notice2">
        <omgdc:Bounds height="55.0" width="85.0" x="685.0" y="155.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="55.0" width="85.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="notice1" id="Shape-notice1">
        <omgdc:Bounds height="55.0" width="85.0" x="320.0" y="150.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="55.0" width="85.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="notice5" id="Shape-notice5">
        <omgdc:Bounds height="55.0" width="85.0" x="700.0" y="285.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="55.0" width="85.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="delete" id="Shape-delete">
        <omgdc:Bounds height="55.0" width="85.0" x="195.0" y="30.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="55.0" width="85.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="notice7" id="Shape-notice7">
        <omgdc:Bounds height="55.0" width="85.0" x="350.0" y="240.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="55.0" width="85.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="BoundaryEvent1" id="Shape-BoundaryEvent1">
        <omgdc:Bounds height="32.0" width="32.0" x="815.0" y="175.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="25.0" y="55.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="BoundaryEvent2" id="Shape-BoundaryEvent2">
        <omgdc:Bounds height="32.0" width="32.0" x="1255.0" y="195.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="40.0" y="55.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNEdge bpmnElement="_57" id="BPMNEdge__57" sourceElement="notice5" targetElement="CreatorModify">
        <omgdi:waypoint x="700.0" y="310.0"/>
        <omgdi:waypoint x="115.0" y="310.0"/>
        <omgdi:waypoint x="115.0" y="210.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_56" id="BPMNEdge__56" sourceElement="_22" targetElement="notice5">
        <omgdi:waypoint x="980.0" y="193.0"/>
        <omgdi:waypoint x="980.0" y="310.0"/>
        <omgdi:waypoint x="785.0" y="310.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_39" id="BPMNEdge__39" sourceElement="ChangeStatus" targetElement="CreatorModify">
        <omgdi:waypoint x="810.0" y="405.0"/>
        <omgdi:waypoint x="95.0" y="405.0"/>
        <omgdi:waypoint x="95.0" y="210.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_38" id="BPMNEdge__38" sourceElement="timer" targetElement="ChangeStatus">
        <omgdi:waypoint x="1115.0312805773287" y="405.0"/>
        <omgdi:waypoint x="1015.0" y="405.0"/>
        <omgdi:waypoint x="895.0" y="405.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_60" id="BPMNEdge__60" sourceElement="wg1" targetElement="delete">
        <omgdi:waypoint x="240.0" y="161.0"/>
        <omgdi:waypoint x="240.0" y="135.0"/>
        <omgdi:waypoint x="240.0" y="85.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="6.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_42" id="BPMNEdge__42" sourceElement="notice6" targetElement="timer">
        <omgdi:waypoint x="1220.0" y="405.0"/>
        <omgdi:waypoint x="1189.0" y="405.0"/>
        <omgdi:waypoint x="1146.9687194226713" y="405.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_41" id="BPMNEdge__41" sourceElement="AllCancel" targetElement="notice6">
        <omgdi:waypoint x="1262.5" y="227.0"/>
        <omgdi:waypoint x="1262.5" y="370.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_44" id="BPMNEdge__44" sourceElement="notice4" targetElement="AllCancel">
        <omgdi:waypoint x="1140.0" y="185.0"/>
        <omgdi:waypoint x="1175.0" y="185.0"/>
        <omgdi:waypoint x="1215.0" y="185.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_45" id="BPMNEdge__45" sourceElement="_22" targetElement="notice4">
        <omgdi:waypoint x="988.0" y="185.0"/>
        <omgdi:waypoint x="1020.0" y="185.0"/>
        <omgdi:waypoint x="1055.0" y="185.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="-2.0" width="-4.0" x="0.0" y="1.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_48" id="BPMNEdge__48" sourceElement="wg2" targetElement="notice2">
        <omgdi:waypoint x="636.0" y="180.0"/>
        <omgdi:waypoint x="660.0" y="180.0"/>
        <omgdi:waypoint x="685.0" y="180.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="-2.0" width="-2.0" x="0.0" y="1.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_25" id="BPMNEdge__25" sourceElement="SecondApprove" targetElement="_22">
        <omgdi:waypoint x="940.0" y="180.0"/>
        <omgdi:waypoint x="950.0" y="180.0"/>
        <omgdi:waypoint x="961.0" y="180.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_47" id="BPMNEdge__47" sourceElement="notice2" targetElement="SecondApprove">
        <omgdi:waypoint x="770.0" y="185.0"/>
        <omgdi:waypoint x="785.0" y="185.0"/>
        <omgdi:waypoint x="790.0" y="185.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_2" id="BPMNEdge__2" sourceElement="_22" targetElement="DeptApprove">
        <omgdi:waypoint x="975.0" y="166.0"/>
        <omgdi:waypoint x="975.0" y="65.0"/>
        <omgdi:waypoint x="517.5" y="125.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_3" id="BPMNEdge__3" sourceElement="notice7" targetElement="CreatorModify">
        <omgdi:waypoint x="350.0" y="265.0"/>
        <omgdi:waypoint x="125.0" y="265.0"/>
        <omgdi:waypoint x="125.0" y="210.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_4" id="BPMNEdge__4" sourceElement="delete" targetElement="end">
        <omgdi:waypoint x="280.0" y="55.0"/>
        <omgdi:waypoint x="315.0" y="55.0"/>
        <omgdi:waypoint x="360.0312805773287" y="55.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_5" id="BPMNEdge__5" sourceElement="wg2" targetElement="notice7">
        <omgdi:waypoint x="620.0" y="196.0"/>
        <omgdi:waypoint x="620.0" y="270.0"/>
        <omgdi:waypoint x="435.0" y="270.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="3.0" width="0.0" x="0.0" y="13.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_6" id="BPMNEdge__6" sourceElement="DeptApprove" targetElement="wg2">
        <omgdi:waypoint x="590.0" y="180.0"/>
        <omgdi:waypoint x="595.0" y="180.0"/>
        <omgdi:waypoint x="606.0" y="180.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_7" id="BPMNEdge__7" sourceElement="start" targetElement="CreatorModify">
        <omgdi:waypoint x="31.968719422671313" y="170.0"/>
        <omgdi:waypoint x="50.0" y="170.0"/>
        <omgdi:waypoint x="65.0" y="170.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_8" id="BPMNEdge__8" sourceElement="BoundaryEvent2" targetElement="ChangeStatus">
        <omgdi:waypoint x="1255.0" y="211.0"/>
        <omgdi:waypoint x="875.0" y="370.0"/>
        <omgdi:waypoint x="875.0" y="375.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_51" id="BPMNEdge__51" sourceElement="notice1" targetElement="DeptApprove">
        <omgdi:waypoint x="405.0" y="177.5"/>
        <omgdi:waypoint x="445.0" y="177.5"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_9" id="BPMNEdge__9" sourceElement="BoundaryEvent1" targetElement="ChangeStatus">
        <omgdi:waypoint x="835.0" y="206.49193338482968"/>
        <omgdi:waypoint x="835.0" y="315.0"/>
        <omgdi:waypoint x="835.0" y="375.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_50" id="BPMNEdge__50" sourceElement="wg1" targetElement="notice1">
        <omgdi:waypoint x="256.0" y="175.0"/>
        <omgdi:waypoint x="300.0" y="175.0"/>
        <omgdi:waypoint x="320.0" y="175.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="-4.0" width="-4.0" x="0.0" y="1.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="_10" id="BPMNEdge__10" sourceElement="CreatorModify" targetElement="wg1">
        <omgdi:waypoint x="185.0" y="175.0"/>
        <omgdi:waypoint x="185.0" y="175.0"/>
        <omgdi:waypoint x="226.0" y="175.0"/>
        <bpmndi:BPMNLabel>
          <omgdc:Bounds height="0.0" width="0.0" x="0.0" y="0.0"/>
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNEdge>
    </bpmndi:BPMNPlane>
  </bpmndi:BPMNDiagram>
</definitions>



public class DataThemeFirstTaskListener implements TaskListener {
    @Override
    public void notify(DelegateTask delegateTask) {
        String firstDepartment = delegateTask.getVariable("firstDepartment").toString();
        delegateTask.addCandidateGroup(firstDepartment);
    }
}

public class DataThemeSecondTaskListener implements TaskListener {
    private static DebugPrn dMsg = new DebugPrn(DataThemeSecondTaskListener.class.getName());
    @Override
    public void notify(DelegateTask delegateTask) {
        dMsg.info("中心管理员审批或数据提供方管理员撤回");
        String centerGroup = SubscriptionEmbBroker.getDefaultImpl().getCenterGroup();
        String firstDept = delegateTask.getVariable("firstDepartment").toString();
        List<String> groups = new ArrayList<String>();
        groups.add(centerGroup);
        groups.add(firstDept);
        delegateTask.addCandidateGroups(groups);
    }
}

public class DataThemeThirdTaskListener implements TaskListener {
    private static DebugPrn dMsg = new DebugPrn(DataThemeThirdTaskListener.class.getName());
    @Override
    public void notify(DelegateTask delegateTask) {
        dMsg.info("订阅撤销");
        String creator = delegateTask.getVariable("creator").toString();
        String deptApproveUser = delegateTask.getVariable("deptApproveUser").toString();
        String centerApproveUser = delegateTask.getVariable("centerApproveUser").toString();
        List<String> users = new ArrayList<String>();
        users.add(creator);
        users.add(deptApproveUser);
        users.add(centerApproveUser);
        delegateTask.addCandidateUsers(users);
    }
}

















