# Dima
evo
//utils.addLogRecord("Transfer started, system status = "+ctx.instance.getValueByField('system_status'),0)
if (ctx.instance.getValueByField('system_status') == 1){
    //utils.addLogRecord("If - ok",0)
    var fieldValue = '';
    var today = new Date();
    var deployResponce = ctx.instance.getValueByField('deploy_responce');
    
    var data = '\'{';
    var fields = ['requestor','streamline','jruby_gui_branch','ruby_gui_branch','app_server_branch','db_save_current','db_expiration_date','db_action',
    'db_source','db_take_from','db_previously_saved','copy_media','db_custom_action'];
    
    for (var i in fields){
        if (!utils.isEmpty(ctx.instance.getValueByField(fields[i]))){
            fieldValue = ctx.instance.getValueByField(fields[i]);
                data += '\"' + fields[i] + '\":\"' + fieldValue + '\",';
                
        }
    }
    data = data.slice(0,-1);
    data += '}\'';
    
    //redmine_user   Xms9Ru64udVXfDb3
    //utils.addLogRecord('test1',0)
    var url = " http://openvpn.srv-002.nasc.dn:12354/redmine_deploy_request/";
    var cmd = "curl -X POST --user redmine_user:Xms9Ru64udVXfDb3 --data " + data + " " + url;
    var cmd_result = utils.cmd.run(cmd);
    ctx.instance.setValueToField('system_status', 2);
    //utils.addLogRecord("Result = "+cmd_result,0)
    var resp = JSON.parse(cmd_result);
    if (resp.status == 'OK'){
        ctx.instance.setValueToField('system_status', 3);
        deployResponce += "\n" + today + " - " + resp.message;
        ctx.instance.setValueToField('deploy_responce', deployResponce);
        var deplId = resp.message.substr(resp.message.length-11);
        ctx.instance.setValueToField('deployment_request_id', deplId);
        var idOfTicket = deplId.split('_');
        var ticketUrl = 'http://srv-002.nasctech.com/co/deploy_request/' + parseInt(idOfTicket[1]) + '/edit'
        ctx.instance.setValueToField('ticket_url', ticketUrl);
    } else if (resp.status == 'NOK'){
        ctx.instance.setValueToField('system_status', 4);
        deployResponce += "\n" + today + " - " + resp.message;
        ctx.instance.setValueToField('deploy_responce', deployResponce);
        ctx.instance.setValueToField('status', 2);
        ctx.instance.setValueToField('last_error', resp.message);
    }
    ctx.instance.setValueToField('last_updated_at',new Date());
    ctx.instance.save();
}
